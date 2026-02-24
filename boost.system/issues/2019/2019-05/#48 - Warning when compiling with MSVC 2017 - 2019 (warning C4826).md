# #48 - Warning when compiling with MSVC 2017 / 2019 (warning C4826) [Closed]

> Username: wyattoday  
> Created at: 2019-05-24 13:35:34 UTC  
> Updated at: 2019-05-24 15:55:43 UTC  
> Closed at: 2019-05-24 15:55:43 UTC  
> Url: https://github.com/boostorg/system/issues/48  

When compiling with the latest Visual Studio (updated to the latest patch release -- 16.1.0) a warning is emitted when compiling anything that depends on system's "error_code.hpp).  
Specifically, this is the warning:  
  
```  
boost\system\error_code.hpp(783,1): warning C4826:  Conversion from 'const boost::system::error_category *' to 'boost::ulong_long_type' is sign-extended. This may cause unexpected runtime behavior.  
```  
  
It has a problem with this line of code in the "`hash_value()`" function:  
  
```  
id = reinterpret_cast<boost::ulong_long_type>( &cat );  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-24 14:45:13 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495661074  

I can't reproduce this warning. I updated VS2019 to 16.1.0, and I'm compiling the following program:  
```  
#include <boost/system/error_code.hpp>  
  
int main()  
{  
	boost::system::error_code ec;  
	hash_value( ec );  
}  
```  
No warning is issued.

---

## Comment 2

> Username: wyattoday  
> Created at: 2019-05-24 14:55:43 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495665363  

Sorry, compile targeting 32-bit. If you still can't reproduce it after that I'll make a tiny example reproducing it.

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2019-05-24 14:57:36 UTC  
> Updated at: 2019-05-24 14:57:59 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495666141  

I can reproduce it (including the header is enough), but I had to explicitly enable that particular warning (/w34826)

---

## Comment 4

> Username: pdimov  
> Created at: 2019-05-24 15:03:36 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495668579  

This warning seems to be off by default, yes.

---

## Comment 5

> Username: wyattoday  
> Created at: 2019-05-24 15:06:08 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495669540  

We're compiling with the C++ 14 standard (`/std:c++14`) and we have the Warning Level set to Level 3 (`/W3`). We didn't explicitly enable this warning.

---

## Comment 6

> Username: Mike-Devel  
> Created at: 2019-05-24 15:09:53 UTC  
> Updated at: 2019-05-24 15:13:16 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495671147  

According to the docs, it is turned of by default: https://docs.microsoft.com/en-us/cpp/preprocessor/compiler-warnings-that-are-off-by-default?view=vs-2019  
  
@wyattoday: Can you have a look at actual command line invocation of the compiler that produced this warning? My guess would be that somewhere in your build file, something is enabling this warning.    
  
(EDIT: the other option is of course that some header that you include before boost system enables the warning via [#pragma warning ...](https://docs.microsoft.com/en-us/cpp/preprocessor/warning?view=vs-2019))  
  
A quick fix would be to change the line into   
  
     id = reinterpret_cast<std::uintptr_t>( &cat );  
  
Not sure what the boost / pre c++11 equivalent would be.

---

## Comment 7

> Username: pdimov  
> Created at: 2019-05-24 15:09:59 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495671186  

The warning is listed in https://docs.microsoft.com/en-us/cpp/preprocessor/compiler-warnings-that-are-off-by-default?view=vs-2019 and is indeed off by default for me. I had to enable it with a pragma for it to appear.

---

## Comment 8

> Username: Mike-Devel  
> Created at: 2019-05-24 15:17:36 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495673879  

Of course my quick-fix could potentially change the value stored in `id` compared to the current code, so maybe it  is not a good Idea... sorry, didn't think that through.

---

## Comment 9

> Username: wyattoday  
> Created at: 2019-05-24 15:31:42 UTC  
> Updated at: 2019-05-24 15:32:47 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495678921  

> [...] and is indeed off by default for me. I had to enable it with a pragma for it to appear.  
  
Set the Warning Level to 3 (`/W3` when compiling) and it will appear. No need to explicitly enable the warning for C4826 (just set the Warning level to 2 or above). This is confirmed in the [docs you listed](https://docs.microsoft.com/en-us/cpp/preprocessor/compiler-warnings-that-are-off-by-default?view=vs-2019).  
  
Yes, Warning Level 3 is not the "default" when creating a project in Visual Studio. However it's a level we're comfortable with.

---

## Comment 10

> Username: Mike-Devel  
> Created at: 2019-05-24 15:40:36 UTC  
> Url: https://github.com/boostorg/system/issues/48#issuecomment-495681943  

@wyattoday : The docs show warnings that are *turned off by default* (turning a warning on/off is a separate mechanism from warning levels - even the level1 warnings on that list won't show by default). Also, I was compiling with W4 and it didn't show. Something has to turn on that warning or a certain class of warnings
