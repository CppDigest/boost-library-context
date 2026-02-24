# #251 - On Windows, `absolute("C:")` returns the current working directory? [Closed]

> Username: emmenlau  
> Created at: 2022-08-12 08:45:41 UTC  
> Updated at: 2022-08-12 09:54:25 UTC  
> Closed at: 2022-08-12 09:53:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/251  

I've just found a strange behavior that seems so unlikely that maybe I'm doing something wrong? I'm using boost 1.80.0 current official release.  
  
When I call `absolute("C:")` it returns my current working directory. Is that expected? Is `C:` not a valid absolute path on Windows?

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-12 09:53:35 UTC  
> Updated at: 2022-08-12 09:54:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/251#issuecomment-1212935012  

"C:" is not an absolute path, it refers to the current path on drive C. Similarly, "C:file.txt" refers to "file.txt" that is in the current path on drive C.
