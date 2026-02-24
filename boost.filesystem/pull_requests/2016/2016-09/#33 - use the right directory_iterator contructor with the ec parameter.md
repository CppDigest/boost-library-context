# #33 use the right directory_iterator contructor with the ec parameter [Closed]

> Username: elfofo  
> Created at: 2016-09-04 09:14:15 UTC  
> Updated at: 2016-11-23 17:07:19 UTC  
> Closed at: 2016-11-23 17:07:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/33  

This fixes [7307](https://svn.boost.org/trac/boost/ticket/7307l) by setting the error code to "access denied".

---

## Comment 1

> Username: Beman  
> Created_at: 2016-11-23 17:07:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/33#issuecomment-262574498  

Hopefully fixed. Here is the commit message: Fix #7307, remove_all(dirname,ec) throws on write protected directories. This is a tough one to test. There are three internal function calls where errors might arise, and it would take too much time to write tests for each of those cases. Someday we will have Titus Winter's mock installable file system, but for now are relying on code inspection rather than testing.  
  
Thanks for the pull request! Although I didn't use it directly, it was helpful in coming up with the eventual fix.  
  
--Beman

---
