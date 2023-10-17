# mkdocs-testing
- This is a test environment for troubleshooting my custom coverpage.

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
    - Result: Failed; no change from test 2.
    - Conclusion: The issue likely lies in one or more of the mkdocs-material features.
 
- Test 4:
    - Using: Mild `mkdocs.yml` but with the `instant` features disabled.
    - Result: TBD
    - Conclusion: TBD
