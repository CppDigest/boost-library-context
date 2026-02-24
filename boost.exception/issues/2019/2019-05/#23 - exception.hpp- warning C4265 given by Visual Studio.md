# #23 - exception.hpp: warning C4265 given by Visual Studio [Closed]

> Username: bersbersbers  
> Created at: 2019-05-10 10:18:07 UTC  
> Updated at: 2022-02-02 06:36:05 UTC  
> Closed at: 2019-10-15 20:37:46 UTC  
> Url: https://github.com/boostorg/exception/issues/23  

This has been reported before (https://svn.boost.org/trac10/ticket/13227), but I am not sure that tracker is still active. The problem is still relevant with boost 1.65.1 and 1.70.  
  
Quoting:  
  
> MSVC falsely report a missing virtual destructor inside exception.hpp Would it be possible to add a "pragma warning (disable:4265)" in this file ? This will *not* happen with the default settings of Visual Studio : it happens when trying to raise the warning level (/we4265 : treat "misssing virtual destructor" as an error)  
  
The above link has reproduction steps, in particular, https://github.com/ivsgroup/boost_warnings_minimal_demo

---

## Comment 1

> Username: zajo  
> Created at: 2019-10-15 20:37:46 UTC  
> Url: https://github.com/boostorg/exception/issues/23#issuecomment-542394006  

Sorry I had missed this. See https://github.com/boostorg/throw_exception/commit/43a57d518cf99fc693eebedefcbaa91074674f54

---

## Comment 2

> Username: Shawn1874  
> Created at: 2022-02-02 01:29:30 UTC  
> Url: https://github.com/boostorg/exception/issues/23#issuecomment-1027490050  

I know this is closed, but the warning / error doesn't appear to be a false report.  Instead of disabling the warning, shouldn't the destructor have been made virtual?

---

## Comment 3

> Username: zajo  
> Created at: 2022-02-02 02:35:26 UTC  
> Url: https://github.com/boostorg/exception/issues/23#issuecomment-1027520381  

It is incorrect for this destructor to be virtual. The destructor is never called via a pointer to the base type.

---

## Comment 4

> Username: Shawn1874  
> Created at: 2022-02-02 05:29:32 UTC  
> Url: https://github.com/boostorg/exception/issues/23#issuecomment-1027600176  

As a general rule classes with virtual methods ought to have a virtual destructor which is the reason for the warning.  It certainly wouldn't hurt anything for the the destructor to be virtual.  There is nothing incorrect about a virtual, protected method.  It seems like it was more of a preference for someone to disable the warning.

---

## Comment 5

> Username: zajo  
> Created at: 2022-02-02 06:36:05 UTC  
> Url: https://github.com/boostorg/exception/issues/23#issuecomment-1027630559  

It is a deliberate design decision. Marking a function virtual indicates dynamic polymorphism. In this design, calling the destructor polymorphically would be a logic error. In C++, this is communicated by declaring the destructor protected and non-virtual.
