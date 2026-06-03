# Observation Schema

Each observation file is named `<YYYY-MM-DDTHH-MM-SS>.md` and uses YAML frontmatter.

## Fields

| Field | Type | Description |
|-------|------|-------------|
| task | string | What the user asked for |
| skill | string | Skill name |
| skill_version | string | Version from CHANGELOG.md |
| success | boolean | Whether the skill run succeeded |
| critical | boolean | Whether the failure was critical |
| timestamp | string | ISO 8601 timestamp |
| duration_seconds | number | Estimated duration |
| tokens_used | number | Estimated token usage |
| error | string | Error description (if failed) |
| user_feedback | string | Optional user feedback |
| files_touched | list | Files created/modified/deleted |
