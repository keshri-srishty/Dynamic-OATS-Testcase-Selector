# Analyze PR Changes

Use the **Change Impact Analyzer** agent to evaluate this pull request.

## Context to provide

Paste the following:

1. **PR diff** (or list of changed files):
   ```
   <paste git diff or file list here>
   ```

2. **PR title and description**:
   ```
   <paste PR title and description here>
   ```

3. **PR comments** (optional):
   ```
   <paste any relevant developer comments>
   ```

## Expected output

The agent will return a JSON object with:
- `impacted_areas` — CPS-API functional areas affected
- `test_suites_to_run` — paths to test suites in `devqa_functional_tests/`
- `feature_flags_touched` — any feature flags enabled/disabled in the diff
- `rationale` — brief explanation of selections

Pass this output directly to the **Test Synthesizer** agent.
