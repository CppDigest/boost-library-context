# #151 - Fix edit_segments suffix inference [Closed]

> Username: alandefreitas  
> Created at: 2022-03-25 16:33:28 UTC  
> Updated at: 2022-03-25 17:00:24 UTC  
> Closed at: 2022-03-25 17:00:24 UTC  
> Url: https://github.com/boostorg/url/issues/151  

Replicating the issue:  
  
```  
url_view const u = parse_relative_ref("/path/to/the/file.txt").value();  
auto se = u.segments(p_.allocator());  
se.replace(se.begin(), "etc");  
BOOST_TEST_EQ(u.string(), "/etc/to/the/file.txt");  
```  
  
Output:  
  
```  
#10943 segments.cpp(138) failed: 'u.string() == "/etc/to/the/file.txt"' ('/etc//to/the/file.txt' == '/etc/to/the/file.txt')  
```  
  
The problem is `url::edit_segments` is inferring the new `{"etc"}` segments need an extra slash when it doesn't.
