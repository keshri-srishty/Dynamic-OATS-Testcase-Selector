# Test Selection Criteria

Guidelines for selecting which existing tests to include in a PR's sanity run.

---

## Primary criteria (must match at least one)

1. **Area match** — The test's `category` tag overlaps with an impacted area from the Change Impact Analyzer output.
2. **Feature flag match** — The test's `setup` block references a feature flag that was toggled in the PR diff.
3. **Endpoint match** — The test's `execution` steps hit an API path (`api` field) that was modified in the PR.

---

## Secondary criteria (used to rank within matched tests)

| Criterion | Preference |
|---|---|
| Execution mode | Prefer `"parallel": "1"` for speed |
| Coverage breadth | Prefer tests that chain multiple steps (enrollment → deployment → validation) |
| Stability | Prefer tests with a known pass history; skip cases marked WIP or with known flakiness |
| Scope | Prefer narrower, targeted cases over broad catch-all cases |

---

## Cap

- **Up to 10 test cases per suite** for a focused change.
- **Up to 25 test cases per suite** for a broad change (e.g., touches enrollment lifecycle, network config, and deployment).

---

## Always include

- At minimum one **positive flow** (happy path) for each impacted area.
- At minimum one **negative flow** (error/rejection) if the PR touches validation or error handling logic.

---

## Geography and RA type matrix

When the change affects network configuration or RA behavior, include tests covering:

| Geography | RA types to cover |
|---|---|
| core | symantec, ecr |
| conus | symantec |
| rglb | symantec |

When the change is RA-specific (e.g., DigiCert), limit to that RA's suite.

---

## Stable suites (always safe to include for broad changes)

- `classic-cc-improvements/active-certificates`
- `classic-cc-improvements/enrollments-with-changes`
- `ECR/ecr_create_order`
- `ECR/ecr_validate_slot`