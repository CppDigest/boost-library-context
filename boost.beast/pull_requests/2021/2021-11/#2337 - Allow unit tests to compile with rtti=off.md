# #2337 Allow unit tests to compile with rtti=off [Closed]

> Username: edtanous  
> Created at: 2021-11-03 19:02:15 UTC  
> Updated at: 2021-11-03 21:42:44 UTC  
> Closed at: 2021-11-03 21:42:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2337  

This struct is only used to make sure that there are no duplication of test suites.  Move it to boost::type_index, which works with RTTI disabled.  
  
Related PR: #2331

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-11-03 19:11:02 UTC  
> Url: https://github.com/boostorg/beast/pull/2337#issuecomment-959840374  

An automated preview of the documentation is available at [https://2337.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html](https://2337.beastdocs.prtest.cppalliance.org/libs/beast/doc/html/index.html)

---
