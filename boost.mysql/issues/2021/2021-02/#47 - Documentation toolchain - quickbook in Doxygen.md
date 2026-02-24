# #47 - Documentation toolchain - quickbook in Doxygen [Closed]

> Username: anarthal  
> Created at: 2021-02-22 19:35:27 UTC  
> Updated at: 2023-02-24 19:20:42 UTC  
> Closed at: 2023-02-24 19:20:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/47  

Docca does not support quickbook in Doxygen comments, which Boost.Mysql uses. ATM we workaround the issue by monkey-patching a file in docca to prevent it from escaping quickbook. Decide and act on what to do with this.
