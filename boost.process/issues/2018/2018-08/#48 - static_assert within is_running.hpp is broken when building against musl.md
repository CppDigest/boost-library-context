# #48 - static_assert within is_running.hpp is broken when building against musl [Closed]

> Username: JCYang  
> Created at: 2018-08-08 08:29:10 UTC  
> Updated at: 2019-04-10 02:15:55 UTC  
> Closed at: 2019-04-10 02:15:55 UTC  
> Url: https://github.com/boostorg/process/issues/48  

when building against musl libc. that assertion failed.  
because musl contain correction for mips.  
details here:  
http://git.musl-libc.org/cgit/musl/commit/include/sys/wait.h?id=41c632824c08ac2c9eea43b30d1b3515dd910df6  
  
so that assertion should be fixed.  
the status code is opaque implementation detail, isn't it? that's why we need those test macro, so this is not reliable and portable at the beginning, iiuc.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-08-08 09:01:01 UTC  
> Url: https://github.com/boostorg/process/issues/48#issuecomment-411337545  

Looks like it, but then I would need a new definition for the `still_running` constant - because, yes, it is an opaque detail. I didn't find a way how to do implement that otherwise, but I'd love suggestions. Otherwise, if you know what `#ifdef` to use, feel free to create a PR for a musl workaround.

---

## Comment 2

> Username: jake-at-work  
> Created at: 2018-09-27 17:24:06 UTC  
> Url: https://github.com/boostorg/process/issues/48#issuecomment-425175131  

@JCYang the fix for #51 may solve this issue too.
