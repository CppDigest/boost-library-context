# #13 - Potential code issue [Closed]

> Username: JaapAap  
> Created at: 2017-06-06 20:09:48 UTC  
> Updated at: 2017-06-10 14:28:52 UTC  
> Closed at: 2017-06-10 14:28:06 UTC  
> Url: https://github.com/boostorg/nowide/issues/13  

I am just reading over the code in iostream.cpp. I don't consider myself an expert though and would not know how to test this at this point, but thought I would just bring this to your attention...  
  
In the function `size_t read()` I notice that a value of -1 can be returned to indicate an invalid codepoint is encountered. Should `read() ` not rather return an integer to be able to represent this flag?  
  
Furthermore, in the one and only location where `read()` is called, there is no check on its return value but it is plugged into `setg` right away as:  
  
```  
            setg(buffer_,buffer_,buffer_+n);  
```  
To me it seems that an illegal codepoint would cause a huge value to be used for the last entry, which is potentially harmful.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-06-06 20:17:14 UTC  
> Url: https://github.com/boostorg/nowide/issues/13#issuecomment-306604296  

Yes you are right. It should be fixed.

---

## Comment 2

> Username: JaapAap  
> Created at: 2017-06-06 20:22:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/13#issuecomment-306605703  

Thanks for the fast reply! I suppose that the handling should be similar as in the `write()` case?  How could this be tested, the current test only deals with nominal behaviour I believe?

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2017-06-06 21:54:21 UTC  
> Url: https://github.com/boostorg/nowide/issues/13#issuecomment-306628730  

It is virtually impossible to test as it requires user to enter invalid UTF-16 from keyboard...

---

## Comment 4

> Username: artyom-beilis  
> Created at: 2017-06-10 14:28:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/13#issuecomment-307568451  

Fixed in 9b3c367868a1efa7caa953f27af5eaccd7db8b68
