# #405 - json::kind convert to string [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 18:13:40 UTC  
> Updated at: 2020-09-24 18:19:27 UTC  
> Closed at: 2020-09-24 18:19:27 UTC  
> Url: https://github.com/boostorg/json/issues/405  

`operator<<` should work for `json::kind`, if anything for debugging at least. And check that it shows up as text in the .natvis file.
