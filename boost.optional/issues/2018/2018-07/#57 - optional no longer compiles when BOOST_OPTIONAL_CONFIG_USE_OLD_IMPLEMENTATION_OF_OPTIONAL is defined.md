# #57 - optional no longer compiles when BOOST_OPTIONAL_CONFIG_USE_OLD_IMPLEMENTATION_OF_OPTIONAL is defined [Open]

> Username: akrzemi1  
> Created at: 2018-07-11 22:33:08 UTC  
> Updated at: 2018-07-11 22:36:00 UTC  
> Url: https://github.com/boostorg/optional/issues/57  



---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-07-11 22:34:21 UTC  
> Url: https://github.com/boostorg/optional/issues/57#issuecomment-404331593  

```c++  
$ c++ -v                                                                                                        ~  
FreeBSD clang version 6.0.1 (tags/RELEASE_601/final 335540) (based on LLVM 6.0.1)  
Target: x86_64-unknown-freebsd12.0  
Thread model: posix  
InstalledDir: /usr/bin  
  
$ cat a.cc  
#define BOOST_OPTIONAL_CONFIG_USE_OLD_IMPLEMENTATION_OF_OPTIONAL  
#include <boost/optional.hpp>  
  
$ c++ -c a.cc -isystem/usr/local/include  
In file included from a.cc:2:  
In file included from /usr/local/include/boost/optional.hpp:15:  
In file included from /usr/local/include/boost/optional/optional.hpp:1462:  
/usr/local/include/boost/optional/detail/optional_reference_spec.hpp:174:40: error: no type named  
      'optional_value_type' in namespace 'boost::optional_detail'  
    optional<typename optional_detail::optional_value_type<typename boost::result_of<F(T&)>::type>::type> ...  
             ~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/optional/detail/optional_reference_spec.hpp:174:94: error: member 'type' declared as a  
      template  
    optional<typename optional_detail::optional_value_type<typename boost::result_of<F(T&)>::type>::type> ...  
                                                                                             ^  
/usr/local/include/boost/optional/detail/optional_reference_spec.hpp:174:98: error: expected ';' at end of  
      declaration list  
    optional<typename optional_detail::optional_value_type<typename boost::result_of<F(T&)>::type>::type> ...  
                                                                                                 ^  
3 errors generated.  
```

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-07-11 22:36:00 UTC  
> Url: https://github.com/boostorg/optional/issues/57#issuecomment-404331908  

This is fixed in develop branch.
