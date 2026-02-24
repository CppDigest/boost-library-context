# #167 - boost::filesystem::space() does not throw an exception under Windows anymore [Closed]

> Username: pkl97  
> Created at: 2020-11-25 13:05:07 UTC  
> Updated at: 2020-11-27 18:40:03 UTC  
> Closed at: 2020-11-26 06:35:33 UTC  
> Url: https://github.com/boostorg/filesystem/issues/167  

In Boost 1.71.0 the program below threw an exception under Windows and Linux due to the non existing path.  
In Boost 1.74.0 the exception under Linux is still there but under Windows the program returns 0.  
  
```  
#include <iostream>  
#include <boost/filesystem.hpp>  
  
int main()  
{  
    try  
    {  
        boost::filesystem::space("DoesNotExist");  
        return 0;  
    }  
    catch (const std::exception& e) {  
        std::cout << e.what() << std::endl;  
    }  
    return 1;  
}  
```

---

## Comment 1

> Username: pkl97  
> Created at: 2020-11-26 07:05:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/167#issuecomment-734116339  

@Lastique Could this change be integrated into the upcoming Boost 1.75 release?

---

## Comment 2

> Username: Lastique  
> Created at: 2020-11-26 07:43:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/167#issuecomment-734131454  

I will try to include it.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-11-26 23:40:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/167#issuecomment-734508356  

Merged to master, should be released with 1.75.

---

## Comment 4

> Username: pkl97  
> Created at: 2020-11-27 18:40:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/167#issuecomment-734949376  

Great, thanks a lot!
