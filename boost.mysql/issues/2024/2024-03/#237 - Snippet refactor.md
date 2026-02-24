# #237 - Snippet refactor [Closed]

> Username: anarthal  
> Created at: 2024-03-15 18:35:20 UTC  
> Updated at: 2024-03-21 22:22:11 UTC  
> Closed at: 2024-03-21 22:22:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/237  

Snippets are currently contained in a single file. It's huge, and forces workarounds as adding the `_t` suffix to some types to avoid clashes. We should split it in a file per section.
