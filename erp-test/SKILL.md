---
name: erp-test
description: >
  Ejecuta tests de módulos Odoo/Som Energia usando destral.
  Automatiza: verificar contenedores, activar pyenv, ejecutar dodestral.
  Trigger: Cuando necesitas ejecutar tests de un módulo Odoo con destral.
metadata:
  author: oriol
  version: "1.0"
---

## When to Use

Usá este skill cuando:
- Necesitás ejecutar tests de un módulo Odoo del proyecto
- Querés automatizar el workflow de testing local
- Estás desarrollando un módulo y necesitás TDD

## Configuración Requerida

Este skill requiere:
1. **Contenedores Docker**: Solo PostgreSQL, MongoDB, Redis (Odoo corre en el host)
2. **pyenv**: Entorno virtual con nombre `erp` (`pyenv virtualenv erp`)
3. **Odoo instalado en host**: En `/home/oriol/somenergia/src/erp/server/bin`

## Workflow

### Paso 1: Verificar Contenedores (solo BBDD)

```bash
docker ps --format "{{.Names}}" | grep -E "postgres|redis|mongo"
```

Contenedores esperados:
- PostgreSQL (src_db_1)
- MongoDB (src_mongo_1)
- Redis (src_redis_1)
- Odoo NO está en Docker — corre en el host

### Paso 2: Activar pyenv

```bash
pyenv activate erp
```

O si está configurado con pyenv-virtualenv:
```bash
source $(pyenv which activate)
```

### Paso 3: Variables de Entorno

Setear según el workflow de GitHub Actions:

```bash
export PYTHONPATH=/home/oriol/somenergia/src/erp/server/bin:/home/oriol/somenergia/src/erp/server/bin/addons
export OPENERP_PRICE_ACCURACY=6
export OORQ_ASYNC=False
export OPENERP_DB_HOST=localhost
export OPENERP_DB_USER=erp
export OPENERP_DB_PASSWORD=erp
export OPENERP_MONGODB_HOST=localhost
export OPENERP_REDIS_URL=redis://localhost:6379/0
```

### Paso 4: Ejecutar destral

```bash
dodestral <database> -m <module_name>
```

**Ejemplo**:
```bash
dodestral test_som_polissa -m som_polissa
```

O con opciones adicionales:
```bash
destral --report-coverage test_som_polissa -m som_polissa
```

## Usage

### Ejecución Básica

```bash
# Activar entorno y ejecutar tests
pyenv activate erp && dodestral test_db -m som_polissa
```

### Script Wrapper Recomendado

Crear un script en `~/bin/run-erp-test`:

```bash
#!/bin/bash
set -e

MODULE=${1:-som_polissa}
DB_NAME=${2:-test_erp}

# Verificar contenedores
echo "=== Verificando contenedores ==="
docker ps --format "{{.Names}}" | grep -E "postgres|redis|mongo" || {
    echo "ERROR: Contenedores no corriendo. Ejecutá 'dockererp' primero."
    exit 1
}

# Activar pyenv
echo "=== Activando entorno erp ==="
eval "$(pyenv init -)"
pyenv activate erp

# Variables
export PYTHONPATH=/home/oriol/somenergia/src/erp/server/bin:/home/oriol/somenergia/src/erp/server/bin/addons
export OPENERP_PRICE_ACCURACY=6
export OORQ_ASYNC=False
export OPENERP_DB_HOST=localhost
export OPENERP_DB_USER=erp
export OPENERP_DB_PASSWORD=erp
export OPENERP_MONGODB_HOST=localhost
export OPENERP_REDIS_URL=redis://localhost:6379/0

# Ejecutar tests
echo "=== Ejecutando tests de $MODULE ==="
dodestral $DB_NAME -m $MODULE
```

## Errores Comunes

| Error | Causa | Solución |
|-------|-------|----------|
| `destral: command not found` | pyenv no activado | `pyenv activate erp` |
| `Connection refused to localhost:5432` | PostgreSQL no corriendo | `docker-compose up -d` (en dir de BBDDs) |
| `Connection refused to localhost:27017` | MongoDB no corriendo | `docker-compose up -d` |
| `Connection refused to localhost:6379` | Redis no corriendo | `docker-compose up -d` |
| `Database does not exist` | DB no creada | destral la crea automáticamente |
| `timeout` | Tests muy lentos | Los tests de Odoo pueden tomar 10+ min |

## Integración con SDD

Este skill se usa en las fases:
- `sdd-apply`: Para verificar que el código implementado pasa los tests
- `sdd-verify`: Para validar contra specs

El test runner detectado es: `dodestral` (custom Odoo test runner)
