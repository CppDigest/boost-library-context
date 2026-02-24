# #4 Fix trac ticket 5403 [Closed]

> Username: avdv  
> Created at: 2014-07-09 06:00:26 UTC  
> Updated at: 2014-07-22 15:55:43 UTC  
> Closed at: 2014-07-22 15:55:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/4  

This resolves a bug where the directory_iterator at the top of the stack  
is invalid (ie. an end iterator) if an error occurs. So, you cannot  
dereference it, and you cannot use any other method (e.g. no_push) on  
it, otherwise std::abort will be called.  
  
Avoid pushing an end iterator onto the directory stack.  
  
[1] https://svn.boost.org/trac/boost/ticket/5403  
[2] https://svn.boost.org/trac/boost/ticket/6821#comment:5

---

## Comment 1

> Username: Beman  
> Created_at: 2014-07-22 15:55:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/4#issuecomment-49758880  

Thanks, Claudio!  
  
`recursive_directory_iterator::increment` has been reorganized, adding an invariant that progress is always made, even if an error is reported by exception or error_code. Although not applied directly because of the reorganization, the code change was incorporated into the reorganized code.  
  
Tickets [5403](https://svn.boost.org/trac/boost/ticket/5403) and [6821](https://svn.boost.org/trac/boost/ticket/6821) have been closed. Please open a new issue and supply test cases if you continue to experience problems.  
  
--Beman

---
