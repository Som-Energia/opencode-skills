# Som Energia OpenCode Skills

Skills personalitzades per a OpenCode utilitzades en el desenvolupament de Som Energia.

## Instal·lació

```bash
mkdir -p ~/.config/opencode/skills
git clone https://github.com/Som-Energia/opencode-skills.git ~/.config/opencode/skills/somenergia
```

O per instal·lar només una skill específica:

```bash
mkdir -p ~/.config/opencode/skills/erp-test
curl -sL https://raw.githubusercontent.com/Som-Energia/opencode-skills/main/erp-test/SKILL.md \
  > ~/.config/opencode/skills/erp-test/SKILL.md
```

## Skills Disponibles

### erp-test

Executa tests de mòduls Odoo/Som Energia utilitzant `destral`.

**Requereix:**
- Docker executant PostgreSQL, MongoDB, Redis
- pyenv amb entorn `erp`
- Odoo instal·lat al host

**Ús:**
```bash
dodestral test_erp -m som_polissa
```

## Contribuir

1. Fes un fork del repo
2. Crea una nova skill en una carpeta pròpia
3. Segueix el format de `SKILL.md`
4. Pull request

## Format de Skill

Cada skill ha de tenir un `SKILL.md` amb:

```yaml
---
name: nom-skill
description: >
  Descripció curta.
  Trigger: Quan s'utilitza aquesta skill.
metadata:
  author: el-teu-nom
  version: "1.0"
---

## When to Use
...

## Configuració Requerida
...

## Workflow
...

## Errors Comuns
...
```