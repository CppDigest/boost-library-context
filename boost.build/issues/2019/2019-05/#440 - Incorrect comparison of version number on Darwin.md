# #440 - Incorrect comparison of version number on Darwin [Closed]

> Username: ldionne  
> Created at: 2019-05-21 16:01:46 UTC  
> Updated at: 2020-04-02 00:31:48 UTC  
> Closed at: 2020-04-02 00:31:48 UTC  
> Url: https://github.com/boostorg/build/issues/440  

Hi,  
  
In [`src/tools/darwin.jam`](https://github.com/boostorg/build/blob/develop/src/tools/darwin.jam#L141), there's the following code:  
  
```  
# - GCC 4.0 and higher in Darwin does not have -fcoalesce-templates.  
if $(real-version) < "4.0.0"  
{  
    flags darwin.compile.c++ OPTIONS $(condition) : -fcoalesce-templates ;  
}  
```  
  
This adds a command-line flag only when the version of the compiler is less than "4.0.0". Now, I don't know much about BJam, however this seems to be doing a simple lexicographical comparison, not a version-like comparison of the strings. As a result, the version string "10.0.0" (for example) ends up comparing less than "4.0.0", and we (incorrectly) add the flag.  
  
I believe that comparison (and the subsequent comparisons too) should be using a version-like comparison function, but I don't know whether that exists in BJam.

---

## Comment 1

> Username: daira  
> Created at: 2020-02-07 13:35:08 UTC  
> Url: https://github.com/boostorg/build/issues/440#issuecomment-583391284  

This is, quite predictably, breaking builds with the latest Xcode SDK that reports itself as gcc 11.0.3.
