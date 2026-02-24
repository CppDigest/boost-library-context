# #487 Use `__apple_build_version__` to detect Apple clang [Merged]

> Username: chrstphrchvz  
> Created at: 2023-10-20 22:22:57 UTC  
> Updated at: 2023-10-25 12:28:29 UTC  
> Merged at: 2023-10-24 23:04:31 UTC  
> Closed at: 2023-10-24 23:04:31 UTC  
> Url: https://github.com/boostorg/config/pull/487  

…since `__APPLE__` is defined by both Apple Clang and LLVM.org Clang on Apple platforms (as mentioned by https://github.com/boostorg/config/pull/39#issuecomment-59946538).  
  
This ensures `BOOST_CLANG_VERSION` is defined correctly when using LLVM.org clang.  
| Compiler               | Before | After  |  
|------------------------|--------|--------|  
| Xcode 14.2 Apple Clang | `140000` | `140000` |  
| LLVM.org Clang 17.0.3  | `140000` | `170003` |  
| LLVM.org Clang 9.0.1   | `40000`  | `90001`  |

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-10-25 12:28:29 UTC  
> Url: https://github.com/boostorg/config/pull/487#issuecomment-1779166245  

While we're here we should probably update the version check for the newer Xcode releases, as in https://github.com/boostorg/config/pull/488.  
  
They had the perfect opportunity to synchronize their version numbers with upstream LLVM starting with 14.0 and they of course didn't take it.  
  
(Unrelated, we might want to add macos-13 to GHA.)

---
