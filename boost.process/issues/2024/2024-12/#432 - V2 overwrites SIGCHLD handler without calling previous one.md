# #432 - V2 overwrites SIGCHLD handler without calling previous one [Open]

> Username: mika-fischer  
> Created at: 2024-12-02 13:56:22 UTC  
> Updated at: 2025-04-14 15:56:12 UTC  
> Url: https://github.com/boostorg/process/issues/432  

We're using boost::process in a library to spawn a process. We use RHEL 8, so no pidfd, unfortunately.  
  
This library is used in an executable that spawns its own child processes and thus installs its own SIGCHLD handler.  
  
It seems that in v2, the boost::process / boost::asio signal handler completely overwrites the application signal handler, which leads to havoc, since the main application does not notice its child processes exiting anymore...  
  
Asio docs for `basic_signal_set` say (https://live.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/basic_signal_set.html#boost_asio.reference.basic_signal_set.multiple_registration_of_signals):   
> The application must not also register a signal handler using functions such as signal() or sigaction().  
  
This makes the use of `basic_signal_set` unacceptable in a library IMO.  
  
In v1, there was some effort, to call the previous signal handler as well: https://github.com/boostorg/process/blob/55e24609671859626caa39c3502fd12df52aee3b/include/boost/process/v1/detail/posix/wait_for_exit.hpp#L68-L69  
  
However, there's also use of asio::signal_set in sigchld_service: https://github.com/boostorg/process/blob/55e24609671859626caa39c3502fd12df52aee3b/include/boost/process/v1/detail/posix/sigchld_service.hpp#L28  
  
In any case, we managed to use v1 for our use-case. But I think it's currently impossible to use boost process v2 for our use case...  
  
Do you agree or am I missing something?  
  
Are there any plans to make v2 usable in a library, where full control over signal handlers cannot be assumed?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-02 14:12:46 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2511653624  

Such a plan can be made now, I guess. It could even land in 1.88.  
  
What exactly do you need? Just disable the use of the signal ad build tine? Would you still want async wait?

---

## Comment 2

> Username: mika-fischer  
> Created at: 2024-12-02 14:26:14 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2511686961  

The most important step would be if use of `::signal` and `::sigaction` (and by extension `asio::signal_set`) could be disabled at compile time. That would make boost::process safe to use in a library.  
  
Then, use of `async_wait` etc. could give a compile error if there's no pidfd support available and signals are disabled.  
  
However, at least polling the exit status should be possible, but I think it is with `running()`.  
  
Keeping `async_wait` without pidfd support and without signal handlers would be nice of course, but would probably involve some kind of polling inside boost::process. I could understand if you don't want that.  
  
Thanks for considering!

---

## Comment 3

> Username: mika-fischer  
> Created at: 2024-12-02 14:34:06 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2511707238  

Maybe signalfd could also be an option on Linux? It's much older than pidfd... https://man7.org/linux/man-pages/man2/signalfd.2.html

---

## Comment 4

> Username: mika-fischer  
> Created at: 2024-12-02 15:02:57 UTC  
> Updated at: 2024-12-02 15:04:04 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2511781175  

No, seems like signalfd is not a viable option: https://ldpreload.com/blog/signalfd-is-useless?reposted-on-request  
  
AFAIUI the fd only gets signal notifications if the signal is masked for the normal signal handling, which defeats the purpose for this use-case...

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-12-02 15:06:49 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2511790896  

I think a pipe trick is better. Open a pipe, close the write side om the parent and asynchronously wait for it.  
  
Would you be available for beta testing?

---

## Comment 6

> Username: mika-fischer  
> Created at: 2024-12-02 15:28:02 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2511849663  

> I think a pipe trick is better. Open a pipe, close the write side om the parent and asynchronously wait for it.  
  
OK, sounds also good.  
  
> Would you be available for beta testing?  
  
Yes, but I'll be on vacation after this week until next year.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2024-12-03 00:02:22 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2513228369  

Must've been a stressful year if you got 20 days for Weihnachtsurlaub left.   
  
I'll probably get to work in January or Februrary, it won't make the upcoming release (1.87).

---

## Comment 8

> Username: mika-fischer  
> Created at: 2024-12-03 08:41:12 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2513877708  

Indeed 😅  
  
Allright, I'll keep an eye out for it and test it then!

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2024-12-20 12:57:54 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2556956913  

Merry early Christmas: https://github.com/boostorg/process/compare/no-signal-set?expand=1  
  
Well, if it works that is.

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2025-04-14 15:56:11 UTC  
> Url: https://github.com/boostorg/process/issues/432#issuecomment-2802172957  

Sorry, I didn't merge in time for the 1.88 release. Should be in the next.
