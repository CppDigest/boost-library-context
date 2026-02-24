# #1532 Jamfile cleanup [Closed]

> Username: djarek  
> Created at: 2019-03-20 20:59:50 UTC  
> Updated at: 2019-09-10 21:04:02 UTC  
> Closed at: 2019-03-20 21:44:46 UTC  
> Url: https://github.com/boostorg/beast/pull/1532  

- Don't set the global link settings (`<link>`) to avoid dependency  
ordering issues in the future.  
- Allow the user to decide on the link settings for dependencies  
(filesystem/coroutine).  
- Make dependencies in examples explicit.  
- Remove dependency of examples on Boost.Filesystem (was not used there).  
- Deduplicate build settings.  
- Speed up test compilation by avoiding rebuilding of main test file.

---
