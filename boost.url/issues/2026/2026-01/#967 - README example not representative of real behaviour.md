# #967 - README example not representative of real behaviour [Closed]

> Username: wwotex  
> Created at: 2026-01-24 13:12:54 UTC  
> Updated at: 2026-01-27 16:56:03 UTC  
> Closed at: 2026-01-27 16:56:03 UTC  
> Url: https://github.com/boostorg/url/issues/967  

In the `README.adoc` file lines `236-246` the example shows a bunch of assert statements.  
  
```c++  
url_view u( "https://user:pass@example.com:443/path/to/my%2dfile.txt?id=42&name=John%20Doe+Jingleheimer%2DSchmidt#page%20anchor" );  
  
...  
  
assert(u.query() == "id=42&name=John Doe Jingleheimer-Schmidt");  
```  
  
The assert statement for the `u.query()` seems to be slightly wrong because it suggests that the expected result would encode `+` signs as spaces but the default output of your library seems to be coherent with RFC 3986 which does not specify such functionality.  
  
So, if I understand it correctly the assert statement should be:  
```c++  
assert(u.query() == "id=42&name=John Doe+Jingleheimer-Schmidt");  
```  
  
I have tested this using `BOOST_LIB_VERSION == "1_83"`  
  
  
PS..  
  
Apologies, if I've just misunderstood something! :)
