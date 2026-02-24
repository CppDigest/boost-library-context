# #959 - On windows b2 --help prints wrong text. [Closed]

> Username: nanoric  
> Created at: 2024-10-01 17:23:31 UTC  
> Updated at: 2024-11-07 20:41:12 UTC  
> Closed at: 2024-11-07 20:41:12 UTC  
> Url: https://github.com/boostorg/boost/issues/959  

On Windows, b2 --help says:  
```  
  --prefix=<PREFIX>       Install architecture independent files here.  
                          Default: C:\Boost on Windows  
                          Default: /usr/local on Unix, Linux, etc.  
```  
But if the user calls ```b2 install```, only ```C:\Boost\include``` will be installed. The libraries will not be installed into ```C:\Boost\lib```.  
  
Only by calling ```b2 --prefix=C:\Boost install``` will both headers and libraries be installed correctly.

---

## Comment 1

> Username: thtrummer  
> Created at: 2024-11-02 09:25:15 UTC  
> Url: https://github.com/boostorg/boost/issues/959#issuecomment-2452932876  

The help text is actually correct, it just has been broken for the last few releases.
