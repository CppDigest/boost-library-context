# #14 Additions II. [Closed]

> Username: klemens-morgenstern  
> Created at: 2016-01-27 07:06:45 UTC  
> Updated at: 2016-01-27 11:36:47 UTC  
> Closed at: 2016-01-27 10:19:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/14  

@Lastique , I hope that's more acceptable, I've incorporated most of the changes.  
  
In order to keep you from premature pull requests, where the author just copied stuff, he tested - obviously incomplete - in another context, I've added a travis.yml. You can see the results here: [![res](https://travis-ci.org/klemens-morgenstern/winapi.svg)](https://travis-ci.org/klemens-morgenstern/winapi).  
  
The constants in show_windows are related to the [ShowWindow](https://msdn.microsoft.com/en-us/library/windows/desktop/ms633548%28v=vs.85%29.aspx) function. I actually only need the SW_Hide constant in STARTUPINFOA_, that's why there's no actual showWindow function imported.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 07:50:10 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175469039  

Currently testet with MSVC-14.0 and mingw-w64 5.2.0 (x32 & x64)

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-01-27 10:19:08 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175530130  

Looks better. Still, there are several issues:  
- Why does `GetSystemDirectory_.hpp` have an underscore?  
- In `environment.hpp`, when `BOOST_USE_WINDOWS_H` is not defined, the constants should be conditioned on `BOOST_USE_WINAPI_VERSION` the same way as in the other branch. Ditto `startf.hpp`.  
- `BOOST_DETAIL_HANDLEAPI_HPP_` should probably be `BOOST_DETAIL_HANDLE_INFO_HPP_`.  
- In some headers `extern "C"` should be moved inside the `BOOST_USE_WINDOWS_H` guarded section.  
- I would suggest renaming `named_pipe_api.hpp` to `named_pipes.hpp`.  
- Extract `_OVERLAPPED` forward declaration and `boost::detail::winapi::_OVERLAPPED` definition to a separate `overlapped.hpp` header from `file_management.hpp`. Include that header in `file_management.hpp` and `named_pipes.hpp` and remove the duplicates from the latter.  
- In `named_pipes.hpp`, every function in `boost::detail::winapi` that takes a pointer to a structure must take a pointer to the structure in `boost::detail::winapi`. For instance, every function that accepts `_OVERLAPPED*` in Windows SDK must have an inline counterpart in `boost::detail::winapi` that takes `boost::detail::winapi::_OVERLAPPED*` and performs a cast. Note that `boost::detail::winapi::_OVERLAPPED` is always defined regardless of `BOOST_USE_WINDOWS_H`. No structures can be imported into `boost::detail::winapi` with `using`-declarations or `typedef`s. Otherwise it causes ODR violations.  
- Move the contents of `process_api.hpp` into `process.hpp`.  
- Remove `static` from function declarations. See `create_process`, for example.  
- Why is `handles.hpp` included in `process_info.hpp`?  
- I would suggest renaming `shell_api.hpp` to `shell.hpp`.  
- Missing inline wrappers for `SHGetFileInfoA` and `SHGetFileInfoW`.  
- I would suggest renaming `show_windows.hpp` to `ShowWindow.hpp` and adding the declaration of the `ShowWindow` function to the header.  
- What is `startf.hpp`? I could only find these constants in relation to `_STARTUPINFO`, in which case they belong to `process.hpp`.  
- In a few places I've seen that indentation is broken. I can't see it in the web interface but if there are tabs, please convert them to spaces (one indentation level is 4 spaces).  
  
Regarding the Travis script, I really have no idea how to use it and if it is correct. I would prefer it to be excluded from this PR. If support for Travis is to be added in the future, it should be done in a separate PR.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 10:35:46 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175542248  

Thanks for the feedback, I'll add that.  
  
GetSystemDirectory_ has an underscore because of the GetSystemDirectory macro (broke the test because then it looked for GetSystemDirectoryW). I could've named it get_system_directory, but thought that would look strange next to GetCurrentProcess etc.   
  
I'll create an PR for the Travis script. It is correct, since you can see it compile all headers - and it also failed with older versions of mine.

---

## Comment 4

> Username: Lastique  
> Created_at: 2016-01-27 10:47:38 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175548866  

> GetSystemDirectory_ has an underscore because of the GetSystemDirectory macro  
  
Ah, I see. I guess, the current naming scheme is broken then. Let it be `get_system_directory.hpp` then. I'll think about converting the rest in due time.  
  
> I'll create an PR for the Travis script. It is correct, since you can see it compile all headers - and it also failed with older versions of mine.  
  
Actually, following the "Build Passing" link shows nothing but the same "Build Passing" icon on a blank page. Not sure if that's what I'm supposed to see.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 11:07:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175562996  

You were supposed to see that: https://travis-ci.org/klemens-morgenstern/winapi

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 11:09:41 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175564149  

Oh btw: if you leave the pull request open, you'll see my future commits here, since I requested the pull of a whole branch. So I wouldn't need to open yet another one, rather the changes could be seen in place.  
  
And since I will work on that at the last on the weekend, it should not become a pr-zombie.

---

## Comment 7

> Username: Lastique  
> Created_at: 2016-01-27 11:16:20 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175566520  

I'm closing the PRs in order to know when you feel the modifications are completed for the next round of review (i.e. when you create the next PR). GitHub doesn't notify about individual commits added to the PR and it certainly doesn't know when the work is ready.

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2016-01-27 11:36:47 UTC  
> Url: https://github.com/boostorg/winapi/pull/14#issuecomment-175573443  

Ah ok, that makes sense. I would've just tagged you in the comments and asked you.

---
