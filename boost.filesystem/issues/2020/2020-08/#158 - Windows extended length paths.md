# #158 - Windows extended length paths [Closed]

> Username: edvalley  
> Created at: 2020-08-25 08:41:29 UTC  
> Updated at: 2022-08-22 14:37:31 UTC  
> Closed at: 2022-08-22 14:37:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/158  

Can we have something like this in boost::filesystem::path?  
  
```  
#ifdef BOOST_POSIX_API  
    path& make_xlength() { return *this; }  
#else // BOOST_WINDOWS_API  
    BOOST_FILESYSTEM_DECL path& make_xlength()  
    {  
        if (this->native().substr(0, 4).compare(L"\\\\?\\"))  
	    *this = path(std::wstring(L"\\\\?\\") + this->native());  
        return *this;  
    }  
#endif  
```  
  
Thanks for your great job.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-09 21:58:27 UTC  
> Updated at: 2022-08-09 22:03:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/158#issuecomment-1209934288  

This isn't as simple as that. The "\\\\?\\" prefix does not simply mean "long path", it actually disables most of the path processing in the Windows API. There are additional requirements applied to the path with this prefix:  
  
- The path must be absolute,  
- It must use preferred separators ("\\"),  
- It must not contain dot (".") and dot-dot ("..") elements,  
- If the path is an UNC path, it must use the "\\\\?\\UNC\\" prefix.  
  
Some of these requirements (specifically, that the path must be absolute) cannot be achieved by the `path` class itself and require an external operation.  
  
In general, Windows seems to be moving towards [enabling long paths by default](https://docs.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=registry), so I would really prefer this prefix stuff to die out in the future.

---

## Comment 2

> Username: edvalley  
> Created at: 2022-08-22 14:37:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/158#issuecomment-1222453061  

I can see it isn't that simple. I agree nothing must be done about this. Thanks.
