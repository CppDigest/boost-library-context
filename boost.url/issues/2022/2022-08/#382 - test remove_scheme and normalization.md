# #382 - test remove_scheme and normalization [Closed]

> Username: alandefreitas  
> Created at: 2022-08-11 19:42:48 UTC  
> Updated at: 2022-10-19 23:38:45 UTC  
> Closed at: 2022-10-19 23:38:45 UTC  
> Url: https://github.com/boostorg/url/issues/382  

url( "yabba:dabba:doo" ).remove_scheme() should leave you with "dabba%3Adoo" not "./dabba:doo"  
  
Test to see what happens when you normalize "dabba%3Adoo".
