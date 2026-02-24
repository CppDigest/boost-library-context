# #65 - Why doesn't shared_library destructor call unload()? [Closed]

> Username: db4  
> Created at: 2023-10-18 08:49:15 UTC  
> Updated at: 2023-11-01 07:04:44 UTC  
> Closed at: 2023-11-01 06:46:39 UTC  
> Url: https://github.com/boostorg/dll/issues/65  

Docs suggest that it should:  
```c++  
    /*!  
    * Destroys the object by calling `unload()`. If library was loaded multiple times  
    * by different instances, the actual DLL/DSO won't be unloaded until  
    * there is at least one instance that references the DLL/DSO.  
    *  
    * \throw Nothing.  
    */  
    ~shared_library() BOOST_NOEXCEPT {}  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2023-11-01 06:46:39 UTC  
> Url: https://github.com/boostorg/dll/issues/65#issuecomment-1788494621  

It does it in base class destructor:  
  
https://github.com/boostorg/dll/blob/482c5b3b033c4480b80def793045d4681b8ab35e/include/boost/dll/detail/posix/shared_library_impl.hpp#L47

---

## Comment 2

> Username: db4  
> Created at: 2023-11-01 07:04:43 UTC  
> Url: https://github.com/boostorg/dll/issues/65#issuecomment-1788509073  

Ahh, how could I overlook this... Sorry for the noise!
