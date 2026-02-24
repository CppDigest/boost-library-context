# #310 - On MSVC, `relative(const path& p, const path& base)` does not work if one path has Windows long path style [Closed]

> Username: emmenlau  
> Created at: 2024-05-20 15:46:50 UTC  
> Updated at: 2024-05-20 22:58:48 UTC  
> Closed at: 2024-05-20 21:48:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/310  

I have just found a curious case, and I'm unsure if this is to be expected? I'm using `relative(const path& p, const path& base)` on Windows with MSVC toolset. I have two paths, of which `p` is more than 260 characters and therefore uses the `\\?\` Windows long path prefix, whereas `base` is shorter and below the limit, and therefore without the `\\?\` Windows long path prefix.  
  
The return value of `relative()` in this case is an empty path.  
  
Should I ensure that paths have a consistent long path prefix (if any)? Or can the library work around this?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-05-20 21:48:50 UTC  
> Updated at: 2024-05-20 22:58:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/310#issuecomment-2121267178  

In terms of Boost.Filesystem, the "\\\\?\\" prefix is part of the root name of the path, and paths with different root names are unrelated. It is impossible to build a relative path between paths referring to different root names.  
  
Boost.Filesystem does not convert between root names, so it will not interpret e.g. "\\\\?\\C:\\" and "C:\\" as equal paths. Since "\\\\?\\" is more than just a marker for "long" paths, such conversion would not be trivial (if even possible), and I do not think Boost.Filesystem should be doing it.
