# #157 - Signal handler API has no way to set SA_RESTART [Closed]

> Username: nomis  
> Created at: 2018-10-29 20:14:23 UTC  
> Updated at: 2020-12-30 00:54:05 UTC  
> Closed at: 2020-12-30 00:54:03 UTC  
> Url: https://github.com/boostorg/asio/issues/157  

I'm handling SIGCHLD using asio while also doing writes to standard output and reads/writes to files.  
  
If I receive SIGCHLD during a `read()` or `write()` then it gets interrupted. The simplest way to deal with this is to set `SA_RESTART` when setting up the signal handler, but there is no support for doing this and it doesn't happen by default.

---

## Comment 1

> Username: nomis  
> Created at: 2018-11-03 10:33:50 UTC  
> Url: https://github.com/boostorg/asio/issues/157#issuecomment-435577231  

I'm going to workaround this by using `ld --wrap=sigaction` to force the use of `SA_RESTART` and cause a compile error if `sigaction` or `SA_RESTART` does not exist.  
  
I'm not convinced that the `read()` and `write()` calls used to pass signals over a pipe to the I/O service are implemented safely if another signal interrupts them, because the return value is never checked for `EINTR`, but this is not easy to test.

---

## Comment 2

> Username: nomis  
> Created at: 2018-11-04 20:10:46 UTC  
> Url: https://github.com/boostorg/asio/issues/157#issuecomment-435701569  

It's a bit fragile to wrap `sigaction` because on some platforms (NetBSD) there's more than one version of it and while the linker is instructed to wrap `sigaction`, the header files have defined it as `__sigaction14`. I need to wrap both versions of the function and linking will fail if `__sigaction14` does not exist.  
  
It would be a lot easier if asio could be configured to use `SA_RESTART`.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:54:02 UTC  
> Url: https://github.com/boostorg/asio/issues/157#issuecomment-752290071  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#646](https://github.com/chriskohlhoff/asio/issues/646).
