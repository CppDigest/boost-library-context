# #25 Added Environment and Job [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-04-15 16:11:11 UTC  
> Updated at: 2016-06-19 16:59:08 UTC  
> Closed at: 2016-06-19 16:59:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/25  

For my boost.process 0.6 version, I need those two headers, which are currently in the boost.process Repository. Though not approved yet, I think it wouldn't hurt to have them in this repository.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-04-24 23:11:05 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-214063840  

Is there a pattern on how to use enumerators? I need to add JOBOBJECTINFOCLASS to the jobs.hpp, but I don't know how that should be integrated into the winapi module, but is an enumerator. I really don't know how this could be solved without causing an ambiguity error.

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-04-25 00:39:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-214076423  

I think you won't be able to add that enumeration to Boost.WinAPI and will have to include windows.h in your code. Enum forward-declarations are allowed only since C++14 (IIRC) ,and Boost.WinAPI is supposed to be compatible with C++03. Without the enum you won't be able to forward-declare the WinAPI function from Windows SDK.

---

## Comment 3

> Username: Lastique  
> Created_at: 2016-04-25 01:04:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-214078672  

Actually, enum forward-declarations are allowed since C++11, but that won't help as the enum does not have a fixed underlying type in Windows SDK anyway.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2016-04-25 02:05:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-214093062  

Darn, though actually, the forward declaration can work the following way.   
  
``` cpp  
enum _JOBOBJECTINFOCLASS : typename boost::underlying_type<::boost::detail::winapi::JOBOBJECTINFOCLASS_>::type;  
```  
  
This compiles with msvc, but gcc doesn't like that very much - but maybe I'll find something.   
  
Is the C++03 an absolute requirement or could we make an exception for one header I only use in a C++11 library anyway?

---

## Comment 5

> Username: Lastique  
> Created_at: 2016-04-25 08:41:42 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-214210546  

> Darn, though actually, the forward declaration can work the following way.  
  
I'm not sure that that approach is legitimate since the definition of the enum in the Windows SDK does not specify the underlying type. I'm not sure the two types are considered the same in this case, I'll have to dig into the standard.  
  
> Is the C++03 an absolute requirement or could we make an exception for one header I only use in a C++11 library anyway?  
  
I guess we could add an exception but I'd prefer if the rest of the headers were C++03. Can the enum-related stuff be isolated to a separate header? Does it make a meaningful set of API for a header?

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2016-04-25 17:29:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-214450786  

Well it is needed for one function and it would make sense to have that in the job header. I could also add an #ifdef for the C++ Version so you could include the header into C++03. Or the C++03 version automatically includes Windows.h, so you had the same symbols.  
  
Ok, well I assumed, that two enums declared with the same values would have the same type.   
I'll look if I find a portable solution and look if this is just hacking or matches the standard and get back to you.

---

## Comment 7

> Username: klemens-morgenstern  
> Created_at: 2016-05-02 16:08:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-216278787  

I checked this again, and this will indeed not work. That is because the solution that compiles with msvc is actually non-standard. You can only forward-declare a non-scoped enum with  an underlying type, but if you declare an enum without a type, it will be considered non-fixed. So even if the types match, it won't compile. Maybe I find a subset of the JobObject Features I can use without the enum; if not I'll remove it from this PR altogether.

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2016-05-02 21:55:02 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-216376488  

So, I removed the functions that depend on the enumerator, I might get lucky to find a way to work around them So I'll look into that. Thanks!

---

## Comment 9

> Username: klemens-morgenstern  
> Created_at: 2016-05-30 16:01:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-222520574  

Now, I implemented a workaround for the functions taking the enums in my repository. I would consider this PR complete, I have implemented everything I need. I.e. I would only open another one for bugfixes.

---

## Comment 10

> Username: Lastique  
> Created_at: 2016-06-19 16:59:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/25#issuecomment-227008000  

I'll close this PR for now. If there's still something you would like to merge to Boost.WinAPI, please create a separate PR - one for each topic like environment, jobs, etc.

---
