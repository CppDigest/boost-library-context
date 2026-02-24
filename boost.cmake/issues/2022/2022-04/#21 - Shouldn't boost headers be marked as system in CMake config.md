# #21 - Shouldn't boost headers be marked as system in CMake config? [Closed]

> Username: ixSci  
> Created at: 2022-04-11 07:44:15 UTC  
> Updated at: 2024-07-15 07:09:17 UTC  
> Closed at: 2024-07-15 07:09:17 UTC  
> Url: https://github.com/boostorg/cmake/issues/21  

Right now every boost target provides its headers by setting the following property: `INTERFACE_INCLUDE_DIRECTORIES`. And there is another property: `INTERFACE_SYSTEM_INCLUDE_DIRECTORIES` which, I think, should be used as well. I don't know if that should be default or not but I believe it should at least be supported so users can opt-in on boost library being treated as system.

---

## Comment 1

> Username: kkaefer  
> Created at: 2023-03-24 09:34:00 UTC  
> Url: https://github.com/boostorg/cmake/issues/21#issuecomment-1482506860  

Please don’t do this. System headers are treated as immutable by compilers and are exempt from dependency/mtime checks. That means an update of your Boost header won’t trigger an automatic rebuild in all cases and you’ll spend a few hours or days tracking down why your build fails even though all the files are checked out correctly.

---

## Comment 2

> Username: jzakrzewski  
> Created at: 2023-04-03 10:45:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/21#issuecomment-1494091712  

Compilers do not care about the timestamps.  
For other tool (like ninja) I don't believe it analyzes whether one passes -I/path/to/headers or -isystem /path/to/headers...

---

## Comment 3

> Username: tastyherring  
> Created at: 2024-07-14 07:49:29 UTC  
> Url: https://github.com/boostorg/cmake/issues/21#issuecomment-2227230490  

Marking boosts header as system will allow users to increase the compiler warning level for their projects code and not get flood with warnings cause by boost system headers.  This would be very useful.

---

## Comment 4

> Username: pdimov  
> Created at: 2024-07-14 08:02:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/21#issuecomment-2227233699  

IMPORTED targets always use SYSTEM for their includes by default, even for the INTERFACE_INCLUDE_DIRECTORIES property.  
  
See https://cmake.org/cmake/help/latest/prop_tgt/NO_SYSTEM_FROM_IMPORTED.html

---

## Comment 5

> Username: ixSci  
> Created at: 2024-07-15 06:52:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/21#issuecomment-2227808851  

> IMPORTED targets always use SYSTEM for their includes by default, even for the INTERFACE_INCLUDE_DIRECTORIES property.  
>   
> See https://cmake.org/cmake/help/latest/prop_tgt/NO_SYSTEM_FROM_IMPORTED.html  
  
Very good find, thank you! So this issue makes no sense and should be closed, then. Nice of CMake to have this information buried somewhere you can't easily find  but what can you do.
