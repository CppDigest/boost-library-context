# #11 - Illegal instruction exception [Closed]

> Username: kiwipxl  
> Created at: 2016-12-29 06:50:40 UTC  
> Updated at: 2017-04-29 09:09:33 UTC  
> Closed at: 2017-04-29 09:09:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11  

In backend_windows.hpp: try_init_com (line 83) I'm getting an internal exception in "dbgeng.dll". Here is the exact error message: "Exception thrown at 0x0F46D7BD (dbgeng.dll) in XXXX.exe: 0xC000001D: Illegal Instruction".  
The thing is though, the stack still generates correctly after this, it's just incredibly slow (and not really practically usable for my case), so I'm assuming it's due to this.  
I'll try to mess around with it a bit, but some help here would be great! :)  
  
I'm on windows 10, 64 bit. Also note I'm not really doing anything special here, just calling boost::stacktrace::stacktrace and writing to a stringstream like in the examples.

---

## Comment 1

> Username: kiwipxl  
> Created at: 2016-12-29 08:39:46 UTC  
> Updated at: 2016-12-29 09:14:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11#issuecomment-269598348  

So I found [this](http://stackoverflow.com/questions/1699115/how-do-i-debug-illegal-instruction-exception) question on stack overflow and it is also the exact stack trace I am getting. I assume the answer means the exception doesn't mean much then as it only occurs once? If so, then getting stack traces is still very slow then (roughly a few seconds for ~50 frames) - there surely must be a faster way. I guess that's a separate problem then though.  
  
Edit: Okay, the massive slowdown looks to be caused by try_init_com initialising every single time on get_name, get_source_file, ect rather than just once. I'm not sure if that was intended or not, but it seems to have huge performance implications. Anyway, I've made a small change locally to fix this (just made it static, haha), but just thought I'd let you know if you didn't already.  
Thanks for the library, though!

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-01-03 11:29:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11#issuecomment-270095726  

I've optimized the stacktrace streamings. Could you please test the optimized version (it's in the master branch right now)?

---

## Comment 3

> Username: kiwipxl  
> Created at: 2017-01-04 12:00:18 UTC  
> Updated at: 2017-01-04 12:15:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11#issuecomment-270355759  

Thanks for the changes! Unfortunately, although it does run significantly better than it did before (without my static change), it is still too slow for my needs. I'm trying to generate a stacktrace at 60 fps and with the latest changes, it slows my game down to a couple fps instead. I could generate at 60 fps fine with the old changes _and_ that quick static change I did, but there were noticeable spikes. I just tested making idebug static again, and it seems to generate at 60 fps fine without any spikes, so that's definitely nice. So the problem with try_init_com always re-initialising on every stacktrace still seems to be occurring, which seems to be the reason why it's so slow in the first place.

---

## Comment 4

> Username: apolukhin  
> Created at: 2017-01-04 13:34:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11#issuecomment-270371163  

> I just tested making idebug static again, and it seems to generate at 60 fps fine without any spikes  
  
Is it thread safe to use a single static idebug from multiple different threads? If yes - I could easily make global static idebug the default.

---

## Comment 5

> Username: apolukhin  
> Created at: 2017-01-05 19:16:33 UTC  
> Updated at: 2017-01-05 19:18:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11#issuecomment-270731560  

@kiwipxl could you try to make the `idebug` not only `static`, but also `thread_local`? If that works well, that would be the default for the dll version of the library.  
  
P.S.: there's a small chance that `thread_local static` may also fix the Illegal instruction error.

---

## Comment 6

> Username: apolukhin  
> Created at: 2017-04-29 09:09:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/11#issuecomment-298157363  

Added a  `BOOST_STACKTRACE_USE_WINDBG_CACHED`/`libboost_stacktrace_windbg_cached` macro http://boostorg.github.io/stacktrace/boost_stacktrace/configuration_and_build.html  
  
According to tests the cached version is more than 10 times faster (0.8s vs 9.4s). Concurrent invocations do not cause Illegal instruction errors.
