# #3 Keep root path in canonical [Closed]

> Username: mantiz  
> Created at: 2014-06-11 23:52:42 UTC  
> Updated at: 2014-07-16 13:10:20 UTC  
> Closed at: 2014-07-16 13:10:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/3  

[The assertion](https://github.com/boostorg/filesystem/blob/develop/src/operations.cpp#L850) fails if you call `canonical("../some-directory", "/")`. This is because the root path is removed in the loop if the current segment equals to `..` although the current `result` only contains the root path.  
I think one would expect `/some-directory` as the result.  
  
I don't know if there is a more efficient/elegant solution. Let me know if there is one. :)

---

## Comment 1

> Username: mantiz  
> Created_at: 2014-07-10 13:38:34 UTC  
> Url: https://github.com/boostorg/filesystem/pull/3#issuecomment-48604958  

Sorry, I forgot to create a Ticket, so here it is: https://svn.boost.org/trac/boost/ticket/10187  
I think this may also fix https://svn.boost.org/trac/boost/ticket/9683 but I don't have a Windows machine to test it.

---

## Comment 2

> Username: Beman  
> Created_at: 2014-07-16 13:10:20 UTC  
> Url: https://github.com/boostorg/filesystem/pull/3#issuecomment-49162538  

Fix applied. This did in fact also fix 9683.  
The test cases were rewritten to provide more coverage.  
Thanks for the fix!  
--Beman

---
