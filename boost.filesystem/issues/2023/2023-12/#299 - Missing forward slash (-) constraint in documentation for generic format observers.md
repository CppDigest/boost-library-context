# #299 - Missing forward slash (/) constraint in documentation for generic format observers [Closed]

> Username: ecorm  
> Created at: 2023-12-27 00:24:50 UTC  
> Updated at: 2024-01-04 12:18:20 UTC  
> Closed at: 2024-01-04 12:18:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/299  

It is my understanding that `filesystem::path::generic_string` is supposed to use `/` and only `/` as the directory separator.  
  
The `generic_string` Boost documentation references the [Generic pathname format](https://www.boost.org/doc/libs/release/libs/filesystem/doc/reference.html#generic-pathname-format) section. That section includes the following derivation rules for `directory-separator`:  
  
```  
directory-separator:  
            "/"  
            "/" directory-separator  
            preferred-separator  
            preferred-separator directory-separator  
```  
  
Why is `preferred-separator` allowed as a directory separator? That does not make the format "generic" at all if backslashes can be arbitrarily included in Windows!

---

## Comment 1

> Username: Lastique  
> Created at: 2023-12-27 12:38:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/299#issuecomment-1870270324  

> It is my understanding that `filesystem::path::generic_string` is supposed to use `/` and only `/` as the directory separator.  
  
It is true. It does.  
  
> Why is `preferred-separator` allowed as a directory separator?   
  
`directory-separator` describes *any* character sequence that is considered a directory separator. On Windows, this includes both forward and back slashes.  
  
The documentation of `generic_string` is incomplete, as it should say that the returned string uses `/` for `directory-separator`s.

---

## Comment 2

> Username: ecorm  
> Created at: 2023-12-27 13:14:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/299#issuecomment-1870297174  

> `directory-separator` describes any character sequence that is considered a directory separator. On Windows, this includes both forward and back slashes.  
  
This is very confusing, as it's under the "Generic pathname format" heading, where backslashes are not permitted for `generic_string`.  
  
Perhaps there should be two sets of the derivations rules: one for _any_ pathname format, and another for _generic_ pathname format.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-12-27 15:22:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/299#issuecomment-1870402940  

"Generic pathname format" includes both kinds of directory separators. `generic_string` has an additional restriction to use `/` as separators on top of that.

---

## Comment 4

> Username: ecorm  
> Created at: 2023-12-27 21:46:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/299#issuecomment-1870647071  

I see that "generic pathname format" and `generic_string` are baked into the C++ standard, so I guess it's too late to rename "generic pathname format" to be less confusing.  
  
The C++ standard does indeed include a [note](https://eel.is/c++draft/filesystems#fs.path.generic.obs-1) that `/` is used as the directory separator:  
  
> Generic format observer functions return strings formatted according to the generic pathname format.  
A single slash ('/') character is used as the directory-separator.
