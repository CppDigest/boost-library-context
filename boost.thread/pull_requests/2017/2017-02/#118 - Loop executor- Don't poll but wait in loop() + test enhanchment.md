# #118 Loop executor: Don't poll but wait in loop() + test enhanchment. [Merged]

> Username: Oberon00  
> Created at: 2017-02-28 17:52:16 UTC  
> Updated at: 2017-02-28 18:55:11 UTC  
> Merged at: 2017-02-28 18:55:11 UTC  
> Closed at: 2017-02-28 18:55:11 UTC  
> Url: https://github.com/boostorg/thread/pull/118  

Fixes #117.

---

## Comment 1

> Username: Oberon00  
> Created_at: 2017-02-28 17:53:35 UTC  
> Url: https://github.com/boostorg/thread/pull/118#issuecomment-283114340  

I should mention that I only did a `b2 ts_` on my Windows 10 x64 MSVC 14 2015 platform to confirm that the updated test passes for me, but that's all.

---

## Comment 2

> Username: Oberon00  
> Created_at: 2017-02-28 18:26:40 UTC  
> Updated_at: 2017-02-28 18:47:43 UTC  
> Url: https://github.com/boostorg/thread/pull/118#issuecomment-283122411  

<del>Oh, I just see that the test seems to work just by chance because unless I'm mistaken `~serial_executor` fails do ensure that "The completion of all the closures happen before the completion of the executor destructor. " ([docs](http://www.boost.org/doc/libs/1_63_0/doc/html/thread/synchronization.html#thread.synchronization.executors.ref.serial_executor.destructor)). It seems to just call `close()`.</del> Sorry, I missed the `scoped_thread` member. Works fine as documented 👍

---

## Comment 3

> Username: viboes  
> Created_at: 2017-02-28 18:55:02 UTC  
> Url: https://github.com/boostorg/thread/pull/118#issuecomment-283129231  

Thanks for the PR

---
