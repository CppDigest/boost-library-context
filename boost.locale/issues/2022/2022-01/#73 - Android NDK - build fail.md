# #73 - Android NDK - build fail [Closed]

> Username: An0nyMooUS  
> Created at: 2022-01-31 13:48:26 UTC  
> Updated at: 2022-05-30 13:06:59 UTC  
> Closed at: 2022-05-30 13:06:59 UTC  
> Url: https://github.com/boostorg/locale/issues/73  

```  
lib/boost/libs/locale/src/encoding/codepage.cpp:43:30: error: use of undeclared identifier 'converter_between'; did you mean 'convert_between'?  
                    hold_ptr<converter_between> cvt;  
                             ^~~~~~~~~~~~~~~~~  
                             convert_between  
lib/boost/libs/locale/src/encoding/codepage.cpp:37:29: note: 'convert_between' declared here  
                std::string convert_between(char const *begin,  
                            ^  
lib/boost/libs/locale/src/encoding/codepage.cpp:43:30: error: template argument for template type parameter must be a type  
                    hold_ptr<converter_between> cvt;  
                             ^~~~~~~~~~~~~~~~~  
lib/boost/libs/locale/include\boost/locale/hold_ptr.hpp:17:23: note: template parameter is declared here  
    template<typename T>  
                      ^  
lib/boost/libs/locale/src/encoding/codepage.cpp:69:30: error: use of undeclared identifier 'converter_to_utf'  
                    hold_ptr<converter_to_utf<CharType> > cvt;  
                             ^  
lib/boost/libs/locale/src/encoding/codepage.cpp:95:30: error: use of undeclared identifier 'converter_from_utf'; did you mean 'convert_from'?  
                    hold_ptr<converter_from_utf<CharType> > cvt;  
                             ^~~~~~~~~~~~~~~~~~  
                             convert_from  
lib/boost/libs/locale/src/encoding/codepage.cpp:89:29: note: 'convert_from' declared here  
                std::string convert_from(  
                            ^  
lib/boost/libs/locale/src/encoding/codepage.cpp:95:30: error: template argument for template type parameter must be a type  
                    hold_ptr<converter_from_utf<CharType> > cvt;  
                             ^~~~~~~~~~~~~~~~~~  
lib/boost/libs/locale/include\boost/locale/hold_ptr.hpp:17:23: note: template parameter is declared here  
    template<typename T>  
                      ^  
5 errors generated.  
```

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-04-18 10:00:19 UTC  
> Url: https://github.com/boostorg/locale/issues/73#issuecomment-1101278645  

How did you try to build this library? As far as I can tell this can only happen when neither BOOST_LOCALE_WITH_ICONV nor BOOST_LOCALE_WITH_ICU are defined on non-Windows platforms. And that should never be the case when using B2 as it will hard-error when both are not found/usable.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-05-30 13:06:59 UTC  
> Url: https://github.com/boostorg/locale/issues/73#issuecomment-1141137197  

Fixed by #85 even if this was a legit failure
