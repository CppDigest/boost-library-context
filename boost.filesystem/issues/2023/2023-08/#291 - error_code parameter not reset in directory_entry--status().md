# #291 - error_code parameter not reset in directory_entry::status() [Closed]

> Username: gfgtdf  
> Created at: 2023-08-28 16:06:48 UTC  
> Updated at: 2023-08-28 16:56:08 UTC  
> Closed at: 2023-08-28 16:48:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/291  

The documentation https://www.boost.org/doc/libs/1_83_0/libs/filesystem/doc/reference.html#status specifies   
  
> If, during attribute determination, the underlying file system API reports an error, sets ec to indicate the specific error reported. Otherwise, ec.clear().  
  
  
But it was observed that `ec.clear()` is no longer called in all cases, see for example https://github.com/wesnoth/wesnoth/issues/7866

---

## Comment 1

> Username: Lastique  
> Created at: 2023-08-28 16:27:45 UTC  
> Updated at: 2023-08-28 16:28:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/291#issuecomment-1695988331  

Please, provide a reproducer or at least more information as to what's going on. `ec.clear()` is being called [here](https://github.com/boostorg/filesystem/blob/e65ddb6ef21697970f7d1438f7a46c5233940059/src/operations.cpp#L4282-L4283).

---

## Comment 2

> Username: gfgtdf  
> Created at: 2023-08-28 16:41:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/291#issuecomment-1696010214  

I see i linked the wrong documentation, this issue is about `directory_entry::status()` not the `status()` free  function, I think what i said still applies though: https://www.boost.org/doc/libs/1_83_0/libs/filesystem/doc/reference.html#directory_entry-observers refers to https://www.boost.org/doc/libs/1_83_0/libs/filesystem/doc/reference.html#Error-reporting which states:  
  
> Functions having an argument of type system::error_code& report errors as follows, unless otherwise specified:  
  
> If a call by the implementation to an operating system or other underlying API results in an error that prevents the function from meeting its specifications, the system::error_code& argument is set as appropriate for the specific error. Otherwise, clear() is called on the system::error_code& argument.  
  
Here https://github.com/boostorg/filesystem/blob/e65ddb6ef21697970f7d1438f7a46c5233940059/include/boost/filesystem/directory.hpp#L192 `ec` is not reset when the status is already known, which is a behavior change from boost 1.82

---

## Comment 3

> Username: Lastique  
> Created at: 2023-08-28 16:44:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/291#issuecomment-1696013515  

Oh, sorry, you mean `directory_entry` members. Those are not [documented](https://www.boost.org/doc/libs/1_83_0/libs/filesystem/doc/reference.html#directory_entry-observers) to call `ec.clear()`, but in std::filesystem they are effectively required to clear the error code.  
  
I'll fix this, thanks for the report.

---

## Comment 4

> Username: gfgtdf  
> Created at: 2023-08-28 16:56:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/291#issuecomment-1696029748  

Thanks for fixing this.
