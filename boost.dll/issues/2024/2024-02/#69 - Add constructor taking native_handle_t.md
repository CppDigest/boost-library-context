# #69 - Add constructor taking native_handle_t [Closed]

> Username: manipuladordedados  
> Created at: 2024-02-28 00:44:58 UTC  
> Updated at: 2024-12-21 08:32:55 UTC  
> Closed at: 2024-12-21 08:32:54 UTC  
> Url: https://github.com/boostorg/dll/issues/69  

FreeBSD exposes fdlopen() which allows one to dlopen() from a file descriptor. That's the only way to load plugins in capsicum mode. dlopen() and all functions that try to access the filesystem through pathnames fail under capsicum mode (ENOTCAPABLE).  
  
Boost.Dll doesn't need to add functionality that can only be supported under FreeBSD (fdlopen()), but it should be very useful if the ifdef portability blocks only exist to initialize boost::dll::shared_library and nothing else (a small fdlopen() in my source at the beginning and then the rest of the code is the same for all platforms).

---

## Comment 1

> Username: manipuladordedados  
> Created at: 2024-05-06 21:30:33 UTC  
> Url: https://github.com/boostorg/dll/issues/69#issuecomment-2096957499  

any news?

---

## Comment 2

> Username: manipuladordedados  
> Created at: 2024-07-12 21:16:19 UTC  
> Url: https://github.com/boostorg/dll/issues/69#issuecomment-2226364759  

`fdlopen()` is not just a FreeBSD feature; it also exists in Android's libc (Linux).  
`ANDROID_DLEXT_USE_LIBRARY_FD` and `library_fd` in `android_dlopen_ext()`:  
https://developer.android.com/ndk/reference/structandroid/dlextinfo

---

## Comment 3

> Username: manipuladordedados  
> Created at: 2024-07-18 16:12:10 UTC  
> Url: https://github.com/boostorg/dll/issues/69#issuecomment-2236978119  

It seems that the [musl maintainers](https://www.openwall.com/lists/musl/2023/05/08/4) are also aware of this need, which might add weight to the importance and potential adoption of this interface across different platforms and libraries.  
  
@apolukhin

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-12-21 08:32:54 UTC  
> Url: https://github.com/boostorg/dll/issues/69#issuecomment-2558049950  

Many thanks to all the participants! Merged the PR and now shared_library could be created from native handle.
