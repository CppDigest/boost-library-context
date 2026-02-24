# #90 Xml nullkey [Closed]

> Username: brchrisman  
> Created at: 2021-06-18 19:19:49 UTC  
> Updated at: 2021-08-28 05:56:48 UTC  
> Closed at: 2021-08-28 05:56:48 UTC  
> Url: https://github.com/boostorg/property_tree/pull/90  

Brought the nullkey type in line with 'key'

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-06-21 10:55:41 UTC  
> Url: https://github.com/boostorg/property_tree/pull/90#issuecomment-864937782  

Some tests are failing. Any chance you can take a look?

---

## Comment 2

> Username: brchrisman  
> Created_at: 2021-06-21 16:30:25 UTC  
> Updated_at: 2021-06-21 16:32:00 UTC  
> Url: https://github.com/boostorg/property_tree/pull/90#issuecomment-865177815  

> Some tests are failing. Any chance you can take a look?  
  
Looks like this is the error? `./boost/property_tree/detail/xml_parser_read_rapidxml.hpp:36:73: warning: comparison with string literal results in unspecified behaviour [-Waddress]`  
Will take a look.  There doesn't happen to be a docker container image I can run to get the full compile?

---
