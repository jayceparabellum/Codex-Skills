# tasks.json Reference

Use this compact schema when emitting a Mission Blueprint work graph.

```json
{
  "version": "1.0",
  "mission": "Short mission title",
  "tasks": [
    {
      "id": "T001",
      "title": "Implement primary dashboard layout",
      "type": "design|frontend|backend|data|test|docs|qa|infra|research",
      "description": "Concrete work to perform.",
      "depends_on": [],
      "inputs": ["mission.md", "rubric item R1"],
      "outputs": ["path/to/file.tsx"],
      "acceptance": ["Observable condition that proves this task is done."],
      "verification": ["Command, browser check, unit test, or human review step."],
      "risk": "low|medium|high",
      "owner_hint": "executor|human|specialist"
    }
  ],
  "rubric_map": [
    {
      "criterion_id": "R1",
      "criterion": "Original criterion text.",
      "satisfied_by_tasks": ["T001"],
      "verification": ["How this criterion will be checked."]
    }
  ],
  "assumptions": [
    {
      "id": "A1",
      "text": "Assumption text.",
      "impact": "What changes if this is wrong.",
      "verification": "How to confirm it."
    }
  ]
}
```

Rules:

- Use stable task IDs (`T001`, `T002`, ...).
- Keep dependencies acyclic.
- Every task needs at least one acceptance condition and verification step.
- Every rubric criterion needs at least one mapped task and verification step.
- Use empty arrays instead of omitted arrays.
