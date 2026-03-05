# CPS-API Test Script Format

All OATS test scripts in `devqa_functional_tests/` follow a **dual-file format**: a `.json` data file paired with a `.py` runner file.

---

## JSON data file

Each file is a **JSON array** of test case objects. Each object has exactly one key (the test case name) whose value contains:

```json
[
  {
    "<tc-name> (<tags>) <description>": {
      "category": "<comma-separated tags>",
      "parallel": "1",
      "setup": [
        {
          "feature_flag": {
            "<flag-name>": "on"
          }
        }
      ],
      "execution": [
        {
          "id": "1",
          "method": "POST",
          "store_response": 1,
          "host": "{CPS_API_HOST}:{CPS_API_PORT}",
          "ssl": 0,
          "api": "/cps-api/enrollments",
          "headers": {
            "X-CPS-USERNAME": "achoudhu",
            "X-CPS-ROLE": "internal",
            "X-CPS-READ-ACCESS": "read-write",
            "X-CPS-ALLOWED-CONTEXTS": "1000",
            "X-CPS-CURRENT-CONTEXT": "1000",
            "Content-Type": "application/vnd.akamai.cps.enrollment.v11i+json",
            "Accept": "application/vnd.akamai.cps.enrollment-status.v1+json"
          },
          "data": { },
          "expected": {
            "status_code": 202
          }
        }
      ],
      "teardown": []
    }
  }
]
```

### Key rules

| Field | Rule |
|---|---|
| `category` | Comma-separated tags: area, feature version, test group (e.g. `proxy,cps-6.1.0,validate_slot_1,c1`) |
| `parallel` | `"1"` for parallel, `"0"` for serial |
| `host` | Always `{CPS_API_HOST}:{CPS_API_PORT}` |
| `ssl` | `0` for non-SSL, `1` for SSL |
| `store_response` | `1` to store response for chaining subsequent steps |
| `expected.status_code` | Required. Assert HTTP status code only. |
| `expected.headers` | Optional. Assert specific response headers if needed. |
| Response body assertions | **Only** when a field (e.g., enrollment ID) is needed to chain the next step. |

---

## Python runner file

The `.py` file is always the same boilerplate. Copy from any existing file in the same folder:

```python
import os
import sys
import pytest
sys.path.append(os.path.realpath(os.path.dirname(__file__) + "/../.."))
from utils.test_driver.cps_classic_template import CPSRequestHandler
from utils.common.pytest_utils import generate_tests
import logging

logger = logging.getLogger(__name__)

@pytest.mark.serial
class TestSerial:
    serial_tcs, serial_ids = generate_tests(os.path.realpath(__file__), "serial")
    driver_obj = CPSRequestHandler()

    @classmethod
    def setup_class(scope="session"):
        logging.info("Setup")

    @classmethod
    def teardown_class(scope="session"):
        logging.info("Teardown")

    @pytest.mark.parametrize('test_data', serial_tcs, ids=serial_ids)
    def test_serial(self, test_data):
        for item in test_data:
            self.driver_obj.execute(**test_data[item])


@pytest.mark.parallel
class TestParallel:
    parallel_tcs, parallel_ids = generate_tests(os.path.realpath(__file__), "parallel")
    driver_obj = CPSRequestHandler()

    @classmethod
    def setup_class(scope="session"):
        logging.info("Setup")

    @classmethod
    def teardown_class(scope="session"):
        logging.info("Teardown")

    @pytest.mark.parametrize('test_data', parallel_tcs, ids=parallel_ids)
    def test_parallel(self, test_data):
        for item in test_data:
            self.driver_obj.execute(**test_data[item])
```

If a suite has only parallel or only serial cases, include only the relevant class.

---

## Naming conventions

- **Test case name key**: `tc<N>-(<geography>,<validationType>,<certType>) <description>`
- **File name**: `<feature>_<action>.json` / `<feature>_<action>.py`
- **Generated files**: append `_generated` suffix — e.g., `ecr_validate_slot_generated.json`

---

## What NOT to assert

- Log lines or internal server state
- Specific response body fields (except enrollment/change IDs needed for chaining)
- Timing or latency