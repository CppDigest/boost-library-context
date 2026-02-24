# #85 - Py3 std::string is not converted to 'bytes' [Closed]

> Username: pavelschon  
> Created at: 2016-08-26 21:45:11 UTC  
> Updated at: 2019-12-05 15:12:55 UTC  
> Closed at: 2019-12-05 15:12:55 UTC  
> Url: https://github.com/boostorg/python/issues/85  

In Python 3, _std::string_ is incorrectly converted to python 'str' type. It should be converted to 'bytes' type. Pull request #54 probably solves this issue.   
  
Code of str_test module:  
  
```  
#include <boost/python.hpp>  
  
std::string getString()  
{  
    return "string";  
}  
  
std::wstring getWString()  
{  
    return L"wstring";  
}  
  
BOOST_PYTHON_MODULE( str_test )  
{  
    boost::python::def( "getString",  &getString );  
    boost::python::def( "getWString", &getWString );  
}  
```  
  
First example is Python 2 and shows correct results.  
  
```  
Python 2.7.9 (default, Mar  1 2015, 12:57:24)   
[GCC 4.9.2] on linux2  
Type "help", "copyright", "credits" or "license" for more information.  
>>> from str_test import getString, getWString  
>>> type(getString())  
<type 'str'>  
>>> type(getWString())  
<type 'unicode'>  
```  
  
Second example is Python 3 and shows that std::string is unexpectedly converted to 'str'.  
  
```  
Python 3.4.2 (default, Oct  8 2014, 10:45:20)   
[GCC 4.9.1] on linux  
Type "help", "copyright", "credits" or "license" for more information.  
>>> from str_test import getString, getWString  
>>> type(getString())  
<class 'str'>     # ooops!!! expected bytes there!  
>>> type(getWString())  
<class 'str'>  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-08-26 21:53:14 UTC  
> Url: https://github.com/boostorg/python/issues/85#issuecomment-242861075  

I agree this is an issue, though I'm not yet sure what the right fix is. `std::wstring` is defined as `std::basic_string<wchar_t>`, with `wchar_t` being of non-portable compiler-dependent width. So it isn't even clear what data it can hold, much less what encoding the user choses to store in it.  
  
I think it would be best to not provide a default conversion for it, rather than second-guessing what the user may want / need.

---

## Comment 2

> Username: pavelschon  
> Created at: 2016-08-26 22:15:21 UTC  
> Url: https://github.com/boostorg/python/issues/85#issuecomment-242864985  

I think the issue is not with std::wstring, but std::string.  
  
The following workaround works for me:  
  
```  
#if PY_MAJOR_VERSION >= 3  
object getString()  
{  
    return object( handle<>( PyBytes_FromString( "string" ) ) );  
}  
#else /* python 2.x */  
std::string getString()  
{  
    return "string";  
}  
#endif  
```  
  
Then it produces expected results:  
  
```  
Python 3.4.2 (default, Oct  8 2014, 10:45:20)   
[GCC 4.9.1] on linux  
Type "help", "copyright", "credits" or "license" for more information.  
>>> from str_test import getString, getWString  
>>> type(getString())  
<class 'bytes'>  
>>> type(getWString())  
<class 'str'>  
```

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2016-08-26 22:17:52 UTC  
> Url: https://github.com/boostorg/python/issues/85#issuecomment-242865401  

OK, fair enough. :-)  
Still, I think the handling of `getWString()` is wrong, too, i.e. Boost.Python shouldn't provide a default converter for `std::wstring`.

---

## Comment 4

> Username: tadeu  
> Created at: 2017-01-03 12:53:43 UTC  
> Url: https://github.com/boostorg/python/issues/85#issuecomment-270108238  

I think the handling is already correct in both cases, because `std::string` and `std::wstring` are both supposed to handle _text_, not _bytes_. `std::wstring` is supposed to be text encoded in UTF-16 when `sizeof(wchar_t) == 2` (i.e., in Windows) and in UTF-32 when `sizeof(wchar_t) == 4` (i.e., in Unix with default compiler flags). By extension, the universally accepted encoding for `std::string` would be UTF-8.  
  
In Python 3 philosophy, `bytes` is supposed to be just that, an array of _bytes_, *not* text. Sometimes this array of _bytes_ may be holding an _encoded representation_ of text, but this is just a special case.
