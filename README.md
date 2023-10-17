# mkdocs-testing
- This is a test environment.

### Test Log:
---

- Test 1:
    - Using: Simple `mkdocs.yml` (e.g., [mkdocs-coverpage](https://github.com/tylerdotrar/mkdocs-coverpage)) with minimal Vault.
    - Result: Passed; no errors.
    - Conclusion: Need to use a more robust configuration file.
 
- Test 2:
    - Using: Robust `mkdocs.yml` (e.g., [RGBwiki](https://github.com/tylerdotrar/mkdocs-coverpage)) with minimal Vault.
    - Result: Failed; banner search and lightswitch stopped working after site navigation.
    - Conclusion: The vault is not the issue; there is a dependency or setting that is incompatible.
    
- Test 3:
    - Using: Robust `mkdocs.yml` but with all markdown extensions disabled.
    - Result: TBD
    - Conclusion: TBD
