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
    - Using: Mild `mkdocs.yml` but with all `instant` features disabled.
    - Result: Passed; no errors.
    - Conclusion: The issue stems from the optional `instant` features within mkdocs-material.
 
 - Test 5:
    - Using: Mild `mkdocs.yml` but with only the base `instant` feature enabled.
    - Result: Passed; no errors.
    - Conclusion: The issue stems from either the `instant.prefetch` or `instant.progress` feature.
    
 - Test 6:
    - Usage: Robust `mkdocs.yml` using everything but the `instant.prefetch` feature.
    - Result: Failed; banner search and lightswitch stopped working after site navigation.
    - Conclusion: The issue is being created by the `instant.progress` feature.
        - Error returned in the browser console: `Uncaught ReferenceError: Missing element: expected "[rel=canonical]" to be present`
    
 - Test 7:
    - Usage: Robust `mkdocs.yml` using everything but the `instant.progress` and `instant.prefetch` feature.
    - Result: Failed; no change from test 6.
    - Conclusion: Unexpected.  Revalidating test 5.
    
 - Test 8:
    - Usage: Mild `mkdocs.yml` but with only the base `instant` feature enabled.
    - Result: Failed; meaning test 5 was improperly handled.
    - Conclusion: The issue stems from the root `navigation.instant` feature.
    
 - Test 9:
    - Usage: Robust `mkdocs.yml` using everything but `navigation.instant` features.
    - Result: Passed; no errors.
    - Conclusion: Need to modify the coverpage to support `navigation.instant`.
    
    - Update: The site appears to be working now??? Some weird caching is occuring.
    
 - Test 10:
    - Usage: Same as test 9, but with `- navigation.instant` uncommented.
    - Result: Failed; banner search and lightswitch stopped working after site navigation.
    - Conclusion: Need to modify the coverpage to support `navigation.instant`.
