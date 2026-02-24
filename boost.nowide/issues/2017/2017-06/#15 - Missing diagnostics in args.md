# #15 - Missing diagnostics in args [Closed]

> Username: rob-stewart  
> Created at: 2017-06-19 11:56:56 UTC  
> Updated at: 2019-12-14 19:52:16 UTC  
> Closed at: 2019-12-14 19:52:16 UTC  
> Url: https://github.com/boostorg/nowide/issues/15  

In args.hpp:88-94, in fix_args(), you silently ignore CommandLineToArgvW() errors. According to https://msdn.microsoft.com/en-us/library/windows/desktop/bb776391(v=vs.85).aspx, you should call GetLastError() to determine why CommandLineToArgvW() returns NULL.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-06-19 12:10:44 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309421297  

The question is what do I do about it? I can set some error code but in general I don't think throwing an exception will be expected behavior.

---

## Comment 2

> Username: rob-stewart  
> Created at: 2017-06-19 12:10:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309421309  

In args.hpp:99, in fix_args(), you silently truncate the command line if basic_stackstring::convert() returns a null pointer, which can mean unrecognized characters or there was insufficient buffer space (so the choice of short_stackstring was faulty). Silently discarding command line input is a terrible idea.

---

## Comment 3

> Username: rob-stewart  
> Created at: 2017-06-19 12:15:50 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309422345  

In args.hpp:126-136, in fix_env(), you silently ignore failure to convert the environment block. Library users should be made aware of the failure.

---

## Comment 4

> Username: rob-stewart  
> Created at: 2017-06-19 12:17:46 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309422736  

There must be some mechanism for alerting users to failures. Why don't you think an exception is the expected behavior? I realize that you can't work around these failures, but you can't allow library users to think all's well when it isn't.

---

## Comment 5

> Username: artyom-beilis  
> Created at: 2017-06-19 12:20:09 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309423239  

- Regarding stack_string if there is no enough space - the memory is allocated.  
- Also as the result of the review I'm going to change instead discarding entire string I'll replace invalid characters with U-FFFD replacement character. So virtually there are no cases when conversion will fail.   
- Same will be valid for env. so Unless basic function like GetEnvironmentStringsW fails - which basically means we don't have env. variables.

---

## Comment 6

> Username: artyom-beilis  
> Created at: 2017-06-19 12:24:30 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309424151  

Note: converting invalid characters to U-FFFD is actually consistent with what modern WinAPI does.

---

## Comment 7

> Username: rob-stewart  
> Created at: 2017-06-20 14:13:47 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309768453  

The overarching criteria should be that any WIN32 API failure must be reported; silently not producing the expected result is not acceptable. Using U-FFFD is reasonable where applicable, but library users must still be informed. For example, you might provide a status check that permits users to discover that you had to use U-FFFD in at least one case so users will know when there are issues. (A library user might use that information to alert a human that things didn't go as well as planned and offer the option to continue or quit.)

---

## Comment 8

> Username: artyom-beilis  
> Created at: 2017-06-20 14:23:16 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-309771886  

Reasonable

---

## Comment 9

> Username: Flamefire  
> Created at: 2019-12-13 18:33:36 UTC  
> Url: https://github.com/boostorg/nowide/issues/15#issuecomment-565556258  

I agree with the reasoning that a failed WinAPI call at this place must be handled as an error situation. Also there is nothing one can do about this so in pretty much all cases a termination of the program is justified. So an exception is exactly what should happen here as the program will most likely not be able to run with that. See #29
