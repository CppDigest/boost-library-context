# #135 Further clean and fix the CMLs [Closed]

> Username: Flamefire  
> Created at: 2023-10-17 16:13:58 UTC  
> Updated at: 2024-05-31 14:33:19 UTC  
> Closed at: 2024-05-31 11:12:51 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135  

Don't add C++ requiredment in test CML (added in root CML)  
  
`option` does nothing when the variable is already set.  
  
Also remove some redundancies (especially checks for BOOST_COBALT_IS_ROOT and BOOST_SUPERPROJECT_VERSION inside the "BOOST_COBALT_IS_ROOT"-branch)  
  
Happy to help if any questions.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-11-08 01:20:04 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-1800849928  

I think this is outdated now, what part is still relevant?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-11-08 08:06:45 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-1801279981  

I resolved the conflicts. Please check if that looks ok

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2023-11-09 02:51:59 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-1803081924  

Why are you removing `target_compile_features(boost_cobalt PUBLIC cxx_std_20)` ?

---

## Comment 4

> Username: Flamefire  
> Created_at: 2023-11-09 11:32:19 UTC  
> Updated_at: 2023-11-09 11:33:25 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-1803660660  

I'm only removing the duplicated line which is (erroneously) in the test-CML. The actual correct one is still at https://github.com/boostorg/cobalt/blob/540b6637ef411b45bae7daa4f9f521e7b9338a1e/CMakeLists.txt#L65  
  
Note on "erroneously": This is a requirement of the library so it needs to be set for the library **by the library** not by a user even though the "user" here is the tests of the library.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2024-05-30 15:35:23 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-2139970724  

I removed the duplicate in test, is it ok now?

---

## Comment 6

> Username: Flamefire  
> Created_at: 2024-05-31 08:34:11 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-2141499660  

> I removed the duplicate in test, is it ok now?  
  
Synced and updated to see what is left:  
- Unconditional dependency on Boost.Container (reformatting the other line allows the CMake scanner to work)  
- Superflous `include_directories`: This should be covered by `target_include_directories(boost_cobalt PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/include")` unless you are missing a dependency on `boost_cobalt`

---

## Comment 7

> Username: Flamefire  
> Created_at: 2024-05-31 14:33:18 UTC  
> Url: https://github.com/boostorg/cobalt/pull/135#issuecomment-2142380609  

The change at https://github.com/boostorg/cobalt/pull/135/files#diff-1e7de1ae2d059d21e1dd75d5812d5a34b0222cef273b7c3a2af62eb747f9d20aL76-R78 was important because otherwise the dependency won't be detected by the dependency scanner in the super project build which requires it on its own line, see https://github.com/boostorg/cmake/blob/440dd10f57f86a91c9d0ae23f096f343fdd004cb/include/BoostRoot.cmake#L210  
  
It seems to work though, I guess some dependency pulls it in transitively

---
