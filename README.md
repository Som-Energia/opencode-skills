# Som Energia OpenCode Skills

Skills personalizadas para OpenCode usadas en el desarrollo de Som Energia.

## Instalación

```bash
mkdir -p ~/.config/opencode/skills
git clone https://github.com/Som-Energia/opencode-skills.git ~/.config/opencode/skills/somenergia
```

O para instalar solo una skill específica:

```bash
mkdir -p ~/.config/opencode/skills/erp-test
curl -sL https://raw.githubusercontent.com/Som-Energia/opencode-skills/main/erp-test/SKILL.md \
  > ~/.config/opencode/skills/erp-test/SKILL.md
```

## Skills Disponibles

### erp-test

Ejecuta tests de módulos Odoo/Som Energia usando `destral`.

**Requiere:**
- Docker ejecutando PostgreSQL, MongoDB, Redis
- pyenv con entorno `erp`
- Odoo instalado en host

**Uso:**
```bash
dodestral test_erp -m som_polissa
```

## Contribuir

1. Fork del repo
2. Crear una nueva skill en carpeta propia
3. Seguir el formato de `SKILL.md`
4. Pull request

## Formato de Skill

Cada skill debe tener un `SKILL.md` con:

```yaml
---
name: nombre-skill
description: >
  Descripción corta.
  Trigger: Cuándo se usa esta skill.
metadata:
  author: tu-nombre
  version: "1.0"
---

## When to Use
...

## Configuración Requerida
...

## Workflow
...

## Errores Comunes
...
```