# #425 - Autolink not working in VS2022 vs 17.0.6 [Closed]

> Username: AubreyOlefile  
> Created at: 2022-02-20 17:15:55 UTC  
> Updated at: 2022-11-21 12:43:37 UTC  
> Closed at: 2022-11-21 12:43:37 UTC  
> Url: https://github.com/boostorg/config/issues/425  

Have to specify full bath for each .lib , eg (x64) for it to build for x64 config.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-18 17:31:49 UTC  
> Url: https://github.com/boostorg/config/issues/425#issuecomment-1320322749  

Autolinked libraries are searched in one of the library paths specified in the linker command line. See [/LIBPATH](https://learn.microsoft.com/en-us/cpp/build/reference/libpath-additional-libpath?view=msvc-170). Make sure you added library paths in your project.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-11-21 12:43:37 UTC  
> Url: https://github.com/boostorg/config/issues/425#issuecomment-1322013332  

Closing on the assumption that this is expected behaviour.
