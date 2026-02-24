# #17 - [REG 1.72->1.73] Internal compiler error [Closed]

> Username: orgads  
> Created at: 2020-10-22 13:18:21 UTC  
> Updated at: 2020-12-19 13:54:32 UTC  
> Closed at: 2020-12-19 13:54:32 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/17  

Compiler version: mips64-octeon-linux-gnu-g++ (Cavium Networks Version: 2_0_0 build 95) 4.3.3  
  
test.cpp:  
```  
#include <boost/smart_ptr/detail/shared_count.hpp>  
```  
  
```  
$ mips64-octeon-linux-gnu-g++ -I/usr/src/boost_1_74_0 -c test.cpp  
  
/usr/src/boost_1_74_0/boost/throw_exception.hpp: In instantiation of 'boost::wrapexcept<boost::bad_weak_ptr>':  
/usr/src/boost_1_74_0/boost/throw_exception.hpp:165:   instantiated from 'void boost::throw_exception(const E&) [with E = boost::bad_weak_ptr]'  
/usr/src/boost_1_74_0/boost/smart_ptr/detail/shared_count.hpp:670:   instantiated from here  
/usr/src/boost_1_74_0/boost/throw_exception.hpp:107: internal compiler error: tree check: expected tree that contains 'decl common' structure, have 'template_id_expr' in tsubst_copy_and_build, at cp/pt.c:11027  
```  
  
Broke by dad5cb4ed377b18e7989079b19823dae1dba137d.

---

## Comment 1

> Username: Mike-Devel  
> Created at: 2020-10-22 15:39:18 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/17#issuecomment-714580452  

Looking at the CI (https://travis-ci.org/github/boostorg/throw_exception) and the list of "test compilers" at https://www.boost.org/users/history/version_1_74_0.html, I don't think gcc-4.3.3 is supported by boost anymore.   
I guess a newer compiler isn't available, or upgrading is otherwise not feasible?

---

## Comment 2

> Username: pdimov  
> Created at: 2020-10-22 16:31:13 UTC  
> Updated at: 2020-10-22 16:31:35 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/17#issuecomment-714612659  

The only GCC earlier than 4.4 on Compiler Explorer is 4.1.2, and the problem doesn't show up there: https://godbolt.org/z/9aKba3

---

## Comment 3

> Username: orgads  
> Created at: 2020-10-22 19:18:45 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/17#issuecomment-714707126  

I can't currently upgrade the compiler for this platform, but I solved it by defining `BOOST_NO_EXCEPTIONS` (it was defined for most of the project, but was missing for one product).  
  
You may close it if this compiler is not supported.

---

## Comment 4

> Username: orgads  
> Created at: 2020-10-22 19:19:19 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/17#issuecomment-714707413  

By the way, I also have 4.1.2 and it doesn't reproduce with it.
