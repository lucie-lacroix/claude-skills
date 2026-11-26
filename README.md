# claude-skills

Marketplace de plugins Claude Code contenant mes skills perso, versionnées et synchronisées via GitHub.

## Ajouter la marketplace sur une autre machine

Dans Claude Code :

```
/plugin marketplace add lucie-lacroix/claude-skills
/plugin install mes-skills@claude-skills
```

Le repo est privé : la machine doit être authentifiée sur GitHub (`gh auth login`).

## Ajouter une nouvelle skill

1. Créer `plugins/mes-skills/skills/<nom>/SKILL.md` (nom en kebab-case, identique au dossier, avec frontmatter `name:` et `description:`).
2. Bumper la version dans `plugins/mes-skills/.claude-plugin/plugin.json` **et** `.claude-plugin/marketplace.json`.
3. Mettre à jour le tableau ci-dessous.
4. Commit + push.
5. Sur chaque machine : `/plugin marketplace update claude-skills`.

## Skills du plugin `mes-skills`

| Skill | Description |
|-------|-------------|
| `exemple-skill` | Skill d'exemple à remplacer. |
