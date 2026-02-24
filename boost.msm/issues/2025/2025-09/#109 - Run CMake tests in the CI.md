# #109 - Run CMake tests in the CI [Closed]

> Username: chandryan  
> Created at: 2025-09-18 15:45:42 UTC  
> Updated at: 2025-10-14 19:21:01 UTC  
> Closed at: 2025-10-14 19:21:01 UTC  
> Url: https://github.com/boostorg/msm/issues/109  

We could run the unit tests in the CI with CMake, taking the [workflows from Mp11](https://github.com/boostorg/mp11/blob/ef7608b463298b881bc82eae4f45a4385ed74fca/.github/workflows/ci.yml) as example.  
  
CMake has the benefit of better IDE integration, for example with VS Code.  
  
~Then we could remove `test/Jamfile.v2` and only have to maintain the CMake version for running the tests.  
What's your thought on this @henry-ch ? Do you require the tests to run with b2?~  
As b2 is the main build/test system and it allows more fine-granular settings, it would be a better alternative to look into using the test Jamfile in CMake (via `BoostTestJamfile.cmake`) instead.

---

## Comment 1

> Username: chandryan  
> Created at: 2025-10-14 19:21:01 UTC  
> Url: https://github.com/boostorg/msm/issues/109#issuecomment-3403267509  

We now have a CMake job in the CI. I looked into `BoostTestJamfile.cmake`, it only supports a simple syntax and no test suites in the Jamfile. But I think it's not too bad to have the tests mentioned in two files, we don't add test files very often.
