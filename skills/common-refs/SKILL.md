---
name: common-refs
description: "Internal library of technical references (Python, FastAPI, Postgres, Docker). Do not invoke directly — used as reference by other skills."
disable-model-invocation: true
---

# Shared Technical References

This skill is **not** intended for direct invocation by the agent.

It contains the `references/` directory that other skills reference via relative paths:
- `../_shared/references/postgres/schema.md`
- `../_shared/references/fastapi/routers.md`
- `../_shared/references/python/typing.md`
- etc.

## Purpose
- Centralized storage of technology-specific knowledge
- Avoids duplication across multiple skills
- Updated once, used everywhere

## DO NOT
- Invoke this skill directly for tasks
- Modify references without updating dependent skills
