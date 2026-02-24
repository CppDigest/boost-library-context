# #167 - error: the address of 'msg' will always evaluate as 'true' warning with GCC 4.4 [Closed]

> Username: orgads  
> Created at: 2020-10-20 18:52:59 UTC  
> Updated at: 2020-10-21 22:45:49 UTC  
> Closed at: 2020-10-21 20:41:19 UTC  
> Url: https://github.com/boostorg/container/issues/167  

... when building with `BOOST_NO_EXCEPTIONS`.  
  
I bisected official releases. It worked with 1.65.0, and broke in 1.66.0.  
  
test.cpp:  
```  
#include <boost/container/small_vector.hpp>  
int main() { return 0; }  
```  
  
```  
$ g++ -I/usr/src/boost_1_74_0 -Wall -Werror -DBOOST_NO_EXCEPTIONS test.cpp  
cc1plus: warnings being treated as errors  
In file included from /usr/src/boost_1_74_0/boost/container/new_allocator.hpp:24,  
                 from /usr/src/boost_1_74_0/boost/container/vector.hpp:28,  
                 from /usr/src/boost_1_74_0/boost/container/small_vector.hpp:27,  
                 from test.cpp:1:  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp: In function 'void boost::container::throw_bad_alloc()':  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp:56: error: the address of 'msg' will always evaluate as 'true'  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp: In function 'void boost::container::throw_out_of_range(const char*)':  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp:64: error: the address of 'msg' will always evaluate as 'true'  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp: In function 'void boost::container::throw_length_error(const char*)':  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp:72: error: the address of 'msg' will always evaluate as 'true'  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp: In function 'void boost::container::throw_logic_error(const char*)':  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp:80: error: the address of 'msg' will always evaluate as 'true'  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp: In function 'void boost::container::throw_runtime_error(const char*)':  
/usr/src/boost_1_74_0/boost/container/throw_exception.hpp:88: error: the address of 'msg' will always evaluate as 'true'  
```

---

## Comment 1

> Username: orgads  
> Created at: 2020-10-20 18:59:27 UTC  
> Url: https://github.com/boostorg/container/issues/167#issuecomment-713072896  

The breaking commit is 520dd7cbddfa26245edea719d52161e14e41fa95. Reverting it solves the issue.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-10-21 20:41:29 UTC  
> Url: https://github.com/boostorg/container/issues/167#issuecomment-713865216  

Thanks for the report!

---

## Comment 3

> Username: pdimov  
> Created at: 2020-10-21 21:44:31 UTC  
> Url: https://github.com/boostorg/container/issues/167#issuecomment-713894979  

Why do you assert and abort here instead of using boost::throw_exception?

---

## Comment 4

> Username: igaztanaga  
> Created at: 2020-10-21 22:45:49 UTC  
> Url: https://github.com/boostorg/container/issues/167#issuecomment-713921130  

I don't see any advantage, the user is forced to define a function when BOOST_NO_EXCEPTIONS is active. Current implementation normally will trigger an assertion in debug mode and makes sure the function never returns aborting in case the assertion returns.
