# #837 - Exclude tests from Antora compile commands [Closed]

> Username: alandefreitas  
> Created at: 2024-03-20 14:51:54 UTC  
> Updated at: 2024-08-16 16:51:38 UTC  
> Closed at: 2024-08-16 16:51:38 UTC  
> Url: https://github.com/boostorg/url/issues/837  

The compile commands we use for Antora include tests so that symbols in header-only files are visible to Mrdocs. We should change that to only include source files in `src` and include extra source files including these headers if we have to.
