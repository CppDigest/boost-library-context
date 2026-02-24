# #455 Annotate facade header with IWYU export annotation [Open]

> Username: hzeller  
> Created at: 2025-08-30 15:24:29 UTC  
> Updated at: 2025-08-30 15:24:29 UTC  
> Url: https://github.com/boostorg/asio/pull/455  

Without that annotation, tools such as `clang-tidy` or the `clangd` language server (as well as many other tools) will complain about headers not directly providing a symbol if users include `asio.hpp`; With this annotation, they know.  
  
Documentation IWYU  
https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUPragmas.md#iwyu-pragma-begin_exportsend_exports  
  
Documentation llvm include cleaner/clang-tidy/clangd https://clangd.llvm.org/design/include-cleaner#iwyu-pragmas  
  
The source code in this repository is generated from an upstream repository at https://github.com/chriskohlhoff/asio.  
  
Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.

---
