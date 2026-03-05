# Generate Test Script

Use the **Test Synthesizer** agent to produce test coverage for a specific gap.

## Context to provide

1. **Gap description** (what is not covered):
   ```
   <e.g., "No test for DV enrollment with ECR RA on CONUS geography">
   ```

2. **Related existing test case** (for reference format):
   ```
   <paste an existing JSON test case from devqa_functional_tests/ as a reference>
   ```

3. **Endpoint and expected behavior**:
   ```
   Method: POST
   API: /cps-api/enrollments
   Expected status: 202
   Key headers to assert: Content-Type, Location
   ```

## Constraints (always applied)

- Assert **response status code** and **response headers** only.
- Do **not** validate log lines or response body fields unless needed for test chaining.
- Use `{CPS_API_HOST}` and `{CPS_API_PORT}` — no hardcoded hosts.
- Follow the JSON + Python dual-file format (see `.github/instructions/test-script-format.md`).
- Generated scripts are for review only — do not execute automatically.

## Output

The agent will produce:
- A `.json` file with the new test case(s)
- A `.py` file (standard boilerplate)
- A short summary of what is covered and what assertions are made
