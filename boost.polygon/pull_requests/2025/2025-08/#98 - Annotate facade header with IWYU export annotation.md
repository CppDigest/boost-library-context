# #98 Annotate facade header with IWYU export annotation [Open]

> Username: hzeller  
> Created at: 2025-08-30 14:50:46 UTC  
> Updated at: 2025-12-19 16:50:19 UTC  
> Url: https://github.com/boostorg/polygon/pull/98  

Without that annotation, tools such as `clang-tidy` or the `clangd` language server (as well as many other tools) will complain about headers not directly providing a symbol if users include `polygon.hpp`; With this annotation, they know.  
  
Documentation IWYU  
https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUPragmas.md#iwyu-pragma-begin_exportsend_exports  
  
Documentation llvm include cleaner/clang-tidy/clangd https://clangd.llvm.org/design/include-cleaner#iwyu-pragmas

---

## Comment 1

> Username: maliberty  
> Created_at: 2025-12-19 16:50:19 UTC  
> Url: https://github.com/boostorg/polygon/pull/98#issuecomment-3675790841  

👍 any eta for review?

---
