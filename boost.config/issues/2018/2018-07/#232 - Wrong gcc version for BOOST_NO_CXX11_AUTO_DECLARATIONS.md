# #232 - Wrong gcc version for BOOST_NO_CXX11_AUTO_DECLARATIONS [Closed]

> Username: rdoeffinger  
> Created at: 2018-07-30 18:50:36 UTC  
> Updated at: 2018-07-31 08:18:25 UTC  
> Closed at: 2018-07-31 08:18:24 UTC  
> Url: https://github.com/boostorg/config/issues/232  

BOOST_NO_CXX11_AUTO_DECLARATIONS is currently under // C++0x features in 4.4.n and later  
However gcc 4.4.7 (as in RedHat 6 for example) does not support this feature, error messages:  
thirdparty/boost/boost/test/tree/test_case_template.hpp:172: error: expected nested-name-specifier before 'tuple_t'  
thirdparty/boost/boost/test/tree/test_case_template.hpp:172: error: 'tuple_t' has not been declared  
thirdparty/boost/boost/test/tree/test_case_template.hpp:172: error: expected ';' before '=' token  
thirdparty/boost/boost/test/tree/test_case_template.hpp:172: error: expected primary-expression before '=' token  
thirdparty/boost/boost/test/tree/test_case_template.hpp:172: error: expected primary-expression before ';' token  
thirdparty/boost/boost/test/tree/test_case_template.hpp:173: error: expected nested-name-specifier before 'this_type'  
(and so on)  
  
Or if the macro was meant to refer to the "auto" keyword then an extra one is needed for "using" for type declarations, though not sure that is worth the pain.

---

## Comment 1

> Username: Flast  
> Created at: 2018-07-30 22:50:55 UTC  
> Url: https://github.com/boostorg/config/issues/232#issuecomment-409037506  

It seems that Boost.Config is correct: https://wandbox.org/permlink/uXkdDs1ED4tvFgrq . And also, it seems that Boost.Test already fixed that boostorg/test@9bd8ff4dd0f5914f668bfdf71b2ebf16563221b4 . It'll be released on 1.68.0 release.

---

## Comment 2

> Username: rdoeffinger  
> Created at: 2018-07-31 08:18:24 UTC  
> Url: https://github.com/boostorg/config/issues/232#issuecomment-409136806  

Obviously I should not report bugs at the end of a long day...  
I checked the latest version of the boost config file to check it was not changed, but somehow didn't even think of checking boost test for changes...  
Sorry for the noise, closing!
