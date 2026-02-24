# #59 - Cannot compile with musl [Closed]

> Username: thejohnfreeman  
> Created at: 2018-12-14 05:06:36 UTC  
> Updated at: 2018-12-14 05:13:25 UTC  
> Closed at: 2018-12-14 05:13:25 UTC  
> Url: https://github.com/boostorg/process/issues/59  

Compilation with musl breaks on either (or both) line 21 and 22 in [`detail/posix/is_running.hpp`](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/is_running.hpp#L21-L22):  
  
```cpp  
// Use the "stopped" state (WIFSTOPPED) to indicate "not terminated".  
// This bit arrangement of status codes is not guaranteed by POSIX, but (according to comments in  
// the glibc <bits/waitstatus.h> header) is the same across systems in practice.  
constexpr int still_active = 0x017f;  
static_assert(!WIFEXITED(still_active), "Expected still_active to not indicate WIFEXITED");  
static_assert(!WIFSIGNALED(still_active), "Expected still_active to not indicate WIFSIGNALED");  
```  
  
Excerpted from [musl's mailing list](https://www.openwall.com/lists/musl/2018/08/10/3):  
  
> What they're testing for, based on the name still_active, seems to be  
that they have a fake status value they can use that doesn't evaluate  
true for either of these WIF* macros. And their assertion has  
uncovered a bug in boost, because 127 _is a valid signal number_ (on  
mips).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-12-14 05:13:25 UTC  
> Url: https://github.com/boostorg/process/issues/59#issuecomment-447215892  

Duplicate of #48
