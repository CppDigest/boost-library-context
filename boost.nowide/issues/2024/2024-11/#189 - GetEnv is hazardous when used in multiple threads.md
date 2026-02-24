# #189 - GetEnv is hazardous when used in multiple threads [Closed]

> Username: giriel  
> Created at: 2024-11-29 08:12:16 UTC  
> Updated at: 2024-12-20 13:36:14 UTC  
> Closed at: 2024-12-20 13:36:14 UTC  
> Url: https://github.com/boostorg/nowide/issues/189  

Hi all,  
  
While using this library to read environment variables from multiple threads at the same time we noticed crashes and jumbled results.   
  
Looking at the implementation, the static stackstring value is to blame.   
I understand that this is done to keep the non-owning pointer semantics similar to the std version.  
  
But the std version is a bit more thread aware since c++11: https://en.cppreference.com/w/cpp/utility/program/getenv  
  
Should a similar guarantee not be given?   
Or at the very least a big warning be added that you should synchronise access to this function.   
A last option would be to foresee a version that returns the environment value as an owning buffer.   
  
Kind regards,  
Robin

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-11-29 09:15:13 UTC  
> Url: https://github.com/boostorg/nowide/issues/189#issuecomment-2507388441  

Thanks for opening the issue and you are right: In addition to the value returned possibly being modified by `putenv` etc. different threads calling `getenv` might corrupt the returned data. This is at least documented:  
  
>     /// This function is not thread safe or reenterable as defined by the standard library  
  
Give the library requires C++11 now it can be improved to be at least thread-safe.

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2024-11-29 15:21:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/189#issuecomment-2508023913  

But making getenv thread safe on windows only can lead to unexpected behavior. On posix systems or normal ansi or wide versions of getenv aren't thread safe.  
  
What good would it do making it thread safe on single platform creating unexpected behavior

---

## Comment 3

> Username: Flamefire  
> Created at: 2024-11-29 16:53:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/189#issuecomment-2508148056  

It already is thread-safe on non-Windows which was the reason of the original report as far as I understood:  
  
> This function is thread-safe (calling it from multiple threads does not introduce a data race) as long as no other function modifies the host environment. In particular, the POSIX functions [setenv()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/setenv.html), [unsetenv()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/unsetenv.html), and [putenv()](https://pubs.opengroup.org/onlinepubs/9699919799/functions/putenv.html) would introduce a data race if called without synchronization. (since C++11)  
  
So this to me makes the behaviour consistent

---

## Comment 4

> Username: artyom-beilis  
> Created at: 2024-11-30 22:16:04 UTC  
> Url: https://github.com/boostorg/nowide/issues/189#issuecomment-2509401623  

I understand what you mean. getenv should be safe as long as there is no race condition.  
  
Yes, I got you. You are correct
