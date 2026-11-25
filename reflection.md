# Reflection

**1. Which issues were the easiest to fix, and which were the hardest?**
The Flake8 issues were the easiest. Each one was a one-character or one-line change (a space, a comma, two blank lines). The renaming warnings from Pylint were also mechanical, but I had to change every call site consistently. The hardest was `pingHub`: simply deleting `shell=True` was not enough, because I also had to think about what input is legitimate and validate it with `ipaddress`.

**2. Did the tools report any false positives, or any issues you chose not to fix?**
Not exactly false positives, but Bandit's B404 (importing `subprocess`) is only informational. The import is needed, so I kept it and documented why with `# nosec B404`. Bandit's B603/B607 notes on the safe `ping` call were suppressed the same way, since the arguments are a fixed list and the address is validated. A `nosec` should always come with a reason, otherwise it just hides real problems.

**3. How could static analysis be integrated into a real development workflow?**
Run Flake8 and Pylint as a pre-commit hook so style problems never reach the repository, and run all three tools in a CI pipeline (for example GitHub Actions) that fails the build on Bandit High/Medium findings or a Pylint score below a set threshold. Editors can also show the warnings live while coding.

**4. What tangible improvements did you see in code quality, readability and security?**
Pylint went from 4.91 to 10.00 and Bandit from a High and a Medium finding to none. The code is now consistently named and documented, which makes it easier to read and hand over. The shared mutable `history` default and the over-broad `except` were real bugs waiting to happen. Removing `shell=True` and `mktemp` and using `secrets` for the OTP closed genuine security holes, not just style complaints.
