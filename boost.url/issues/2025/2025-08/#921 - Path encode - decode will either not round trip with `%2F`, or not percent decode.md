# #921 - Path encode / decode will either not round trip with `%2F`, or not percent decode [Closed]

> Username: Ericson2314  
> Created at: 2025-08-26 15:46:39 UTC  
> Updated at: 2025-08-26 22:45:00 UTC  
> Closed at: 2025-08-26 22:45:00 UTC  
> Url: https://github.com/boostorg/url/issues/921  

This is significant because `%2F` and `/` mean different things, e.g. in the presence of resolving relative URLs with `..`.  
  
It would be nice to code method to get/set  path as a vector of strings, which would avoid the ambiguity and thus be lossless.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-08-26 16:05:15 UTC  
> Updated at: 2025-08-26 16:05:30 UTC  
> Url: https://github.com/boostorg/url/issues/921#issuecomment-3224826547  

OK. I replicated it:  
  
```cpp  
// https://gitlab.example.com/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz  
url u("https://gitlab.example.com/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST_CSTR_EQ(u.buffer(), "https://gitlab.example.com/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST(u.encoded_path() == "/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST(u.path() == "/api/v4/projects/group/subgroup/project/repository/archive.tar.gz");  
  
// set_encoded_path with encoded value (should round-trip correctly)  
u.set_encoded_path(u.encoded_path());  
BOOST_TEST_CSTR_EQ(u.buffer(), "https://gitlab.example.com/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST(u.encoded_path() == "/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST(u.path() == "/api/v4/projects/group/subgroup/project/repository/archive.tar.gz");  
  
// set_path with decoded value (impossible to round-trip)  
u.set_path(u.path());  
BOOST_TEST_CSTR_EQ(u.buffer(), "https://gitlab.example.com/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST(u.encoded_path() == "/api/v4/projects/group%2Fsubgroup%2Fproject/repository/archive.tar.gz");  
BOOST_TEST(u.path() == "/api/v4/projects/group/subgroup/project/repository/archive.tar.gz");  
```  
  
The encoded values round-trip fine. The decoded value (like all decoded values) cannot be rounded-tripped because it loses information. I recall discussing this when we were designing it. The user has this responsibility when calling the decoded functions because there's no other option.  
  
Nowadays, I think the best option would be not providing any decoding function that includes a delimiter because it's a safety issue. I remember proposing that at the time, but I think it was considered too radical.  
  
I'll change the example to make it more generic, add a comment to the javadoc of `path`, and add an admonition to the exposition of the decoded functions.
