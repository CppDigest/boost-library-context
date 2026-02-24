# #455 - Windows Unicode Limitations ought to be documented for boost::process V1 [Open]

> Username: nigels-com  
> Created at: 2025-01-18 22:27:42 UTC  
> Updated at: 2025-01-21 22:16:54 UTC  
> Url: https://github.com/boostorg/process/issues/455  

We have a non-trivial codebase that was recently built on boost::process::child.  
We've come to realise that it works fine on Linux, but will not support running a program with a unicode path on Windows.  
Migrating to V2 does work, but the migration is non-trivial, unfortunately.  
  
If this limitation could be clearer in the documentation I think it would be useful to maintainers downstream.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-01-20 17:02:41 UTC  
> Url: https://github.com/boostorg/process/issues/455#issuecomment-2602909135  

Well, I am about to deprecate v1 and scrap the v1 docs, so it's a bit late. But there is a reason that v2 operates on "utf-8 or gtfo".  
  
On another note: what are the main non-trivial parts of the migration? I wonder if there's something I can do to help there (some compatibility layer or sth). If that information is not suitable for public issues, please e-mail me.

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-01-21 01:15:04 UTC  
> Url: https://github.com/boostorg/process/issues/455#issuecomment-2603437643  

From one point of view the main difference is that v1 is pre-Asio.  
Asio is something we're already using here and there.  
But migrating to v2 for us seems more of an all-in on Asio.  
We can't really resist having the output of the child process line by line in an Asio event loop.

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-01-21 01:22:44 UTC  
> Url: https://github.com/boostorg/process/issues/455#issuecomment-2603443379  

The "utf-8 or gtfo" is something we're all in on, did not realise we needed V2 for that.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2025-01-21 13:57:06 UTC  
> Url: https://github.com/boostorg/process/issues/455#issuecomment-2604808160  

What do you mean by resist? You can just use the sync apis of the pipes. I would highly recommend against it, but it's possible.

---

## Comment 5

> Username: nigels-com  
> Created at: 2025-01-21 22:16:53 UTC  
> Url: https://github.com/boostorg/process/issues/455#issuecomment-2605852273  

We're in the awkward position of having a mixture of Asio and not-Asio paths.  
The reason we hadn't rolled it out fully was a fair amount of refactoring to get all the benefit.  
All that is intended by this ticket is to make it clear broadly, this important benefit of V2.  
We also have parts of the codebase that are pre-C++17 std::filesystem::path, for example.
