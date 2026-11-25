# Issue Table: parcel_tracker.py

Line numbers refer to the **original** file. Ordered by severity (high first).

| Issue | Type | Line(s) | Description | Fix Approach |
|-------|------|---------|-------------|--------------|
| Command injection via `shell=True` (Bandit B602, **High**) | Security | 55 | `pingHub` concatenates `hubAddress` into a shell command, so input like `8.8.8.8; rm -rf /` would run arbitrary commands. | Validate with `ipaddress.ip_address()` and pass an argument list to `subprocess.check_output` without a shell. |
| Insecure temp file `mktemp` (Bandit B306, **Medium**) | Security | 50 | `tempfile.mktemp` only returns a name; another process can create that file first (race condition / symlink attack). | Use `tempfile.mkstemp`, which creates the file atomically, and close the returned descriptor. |
| Weak random for OTP (Bandit B311, Low) | Security | 72 | `random.randint` is predictable and not suitable for a delivery OTP. | Use `secrets.randbelow(9000) + 1000` (still a 4-digit OTP). |
| `subprocess` import (Bandit B404, Low) | Security | 2 | Informational warning that the module is risky. | Kept the import (it is needed), now only called with a fixed list and no shell; annotated with `# nosec B404`. |
| Mutable default argument (Pylint W0102) | Bug | 11 | `history=[]` is created once and shared by every call, so ids from different callers pile up in one list. | Default to `None` and create a new list inside the function. |
| Overly broad exception (Pylint W0718) | Bug | 37 | `except Exception` would hide unrelated errors (e.g. `TypeError`), not just a missing id. | Catch only `KeyError`. |
| Non-snake_case names (Pylint C0103, x14) | Style | 11, 20, 27, 34, 43, 48, 53, 67 and their arguments | `bookParcel`, `trackingId`, `weightKg`, etc. break PEP 8 naming. | Renamed to `book_parcel`, `tracking_id`, `weight_kg`, `advance_status`, `shipping_cost`, `track_parcel`, `is_with_courier`, `label_path`, `ping_hub`, `hub_address`. |
| Missing docstrings (Pylint C0114, C0116, x8) | Style | 1 and every function | Module and all functions are undocumented. | Added a module docstring and a one-line docstring per function. |
| Repeated comparison (Pylint R1714) | Refactor | 45 | `status == "Picked Up" or status == "In Transit"` repeats the variable. | Use `status in ("Picked Up", "In Transit")`. |
| Missing whitespace around operator (Flake8 E225) | Style | 28 | `cost=50` | Changed to `cost = 50`. |
| Inline comment spacing (Flake8 E261) | Style | 71 | Needs two spaces before `#`. | Added the second space. |
| Missing space after comma (Flake8 E231) | Style | 74 | `"...today:",len(parcels)` | Added a space after the comma. |
| No blank lines / no main guard (Flake8 E305) | Style / Design | 75 | `main()` is called at import time, directly after a function with no blank lines. | Added two blank lines and `if __name__ == "__main__":`. |

## Results

| Tool | Before | After |
|------|--------|-------|
| Pylint | 4.91/10 | **10.00/10** |
| Bandit | 1 High, 1 Medium, 2 Low | **0 issues** |
| Flake8 | 4 issues | **0 issues** |

The program output is identical before and after (apart from the random OTP).
