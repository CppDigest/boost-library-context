# #29 - Non-posix header used in /posix subfolder [Closed]

> Username: extrowerk  
> Created at: 2019-07-16 18:22:25 UTC  
> Updated at: 2019-11-11 18:31:30 UTC  
> Closed at: 2019-11-11 18:22:28 UTC  
> Url: https://github.com/boostorg/dll/issues/29  

See:  
https://github.com/boostorg/dll/blob/8f39a38a3188a510054d86ee15bc523494676cdc/include/boost/dll/detail/posix/path_from_handle.hpp#L108  
and:  
https://github.com/boostorg/dll/blob/8f39a38a3188a510054d86ee15bc523494676cdc/include/boost/dll/detail/posix/shared_library_impl.hpp#L23  
  
AFAIK link.h is [not a POSIX header](https://en.wikipedia.org/wiki/C_POSIX_library), Haiku for example have no such header:  
https://github.com/haikuports/haikuports/issues/3998  
  
This causes portability problems.

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-08-25 14:21:40 UTC  
> Url: https://github.com/boostorg/dll/issues/29#issuecomment-524634206  

@extrowerk I'd appreciate help here. Could you make PR with a fix? Or just point me to the header with the required functions.

---

## Comment 2

> Username: extrowerk  
> Created at: 2019-08-25 18:55:43 UTC  
> Url: https://github.com/boostorg/dll/issues/29#issuecomment-524654891  

I just imported the relevant headers from Freebsd to the Haiku source tree, so it isn't a problem for us anymore, but this is still a bug in boost which should be fixed. I can't answer your question, i am not a developer.

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-11-11 18:22:28 UTC  
> Url: https://github.com/boostorg/dll/issues/29#issuecomment-552556568  

Thanks for importing the header! That fixes the issue with Haiku, and no one else complains about the usage of `link.h`.  
  
Headers with `link.h` inclusion are headers with platform specific workarounds. `link.h` is the usually working solution to do the job. There's *no* solution for the problem in POSIX.  
  
In case of errors on other platforms please report them in a separate issues

---

## Comment 4

> Username: extrowerk  
> Created at: 2019-11-11 18:31:29 UTC  
> Url: https://github.com/boostorg/dll/issues/29#issuecomment-552559734  

This is a great example of linuxism, i fear.
