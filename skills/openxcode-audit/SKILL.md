---
name: openxcode-audit
description: >
  Codebase analysis skill for identifying over-engineering, redundant dependencies,
  dead code, and custom abstractions that can be replaced with standard library APIs.
---

# OpenXCode Audit

Perform a systematic codebase review to identify unnecessary complexity and optimization opportunities.

## Findings Categories

- `remove`: Dead functions, obsolete configurations, or unused code branches.
- `stdlib`: Custom utility routines that can be replaced with native standard library functions.
- `platform`: Third-party packages whose functionality is natively available in the platform or runtime.
- `reuse`: Redundant helper implementations that duplicate existing project modules.
- `simplify`: Overly complex abstractions, single-consumer interfaces, or deep inheritance hierarchies that can be flattened.

## Review Focus Areas

1. Third-party packages that duplicate runtime/standard library features.
2. Unnecessary abstraction layers created around single call sites.
3. Premature architectural patterns where standard procedures suffice.
4. Over-engineered fallback logic for unreachable internal states.
