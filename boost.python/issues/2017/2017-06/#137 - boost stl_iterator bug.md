# #137 - boost stl_iterator bug [Closed]

> Username: yiakwy  
> Created at: 2017-06-29 18:43:19 UTC  
> Updated at: 2017-07-20 12:21:28 UTC  
> Closed at: 2017-07-20 12:21:28 UTC  
> Url: https://github.com/boostorg/python/issues/137  

both boost157 and boost 1.64.0_1 cannot compile the init codes by firing:  
  
```C++  
/usr/local/Cellar/boost/1.64.0_1/include/boost/python/stl_iterator.hpp:48:16: error: implicit instantiation of undefined template  
      'boost::python::extract<double>'  
        return extract<ValueT>(this->impl_.current().get())();  
               ^  
/usr/local/Cellar/boost/1.64.0_1/include/boost/iterator/iterator_facade.hpp:549:20: note: in instantiation of member function  
      'boost::python::stl_input_iterator<double>::dereference' requested here  
          return f.dereference();  
                   ^  
/usr/local/Cellar/boost/1.64.0_1/include/boost/iterator/iterator_facade.hpp:655:42: note: in instantiation of function template  
      specialization 'boost::iterators::iterator_core_access::dereference<boost::python::stl_input_iterator<double> >' requested  
      here  
            return iterator_core_access::dereference(this->derived());  
                                         ^  
/Users/wangyi/Downloads/Xcode 2.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/../include/c++/v1/vector:1135:19: note:   
      in instantiation of member function  
      'boost::iterators::detail::iterator_facade_base<boost::python::stl_input_iterator<double>, double,  
      std::__1::input_iterator_tag, double, long, false, false>::operator*' requested here  
        push_back(*__first);  
                  ^  
src/fft_module.cpp:46:23: note: in instantiation of function template specialization 'std::__1::vector<double,  
      std::__1::allocator<double> >::vector<boost::python::stl_input_iterator<double> >' requested here  
        new (storage) Container(  
                      ^  
src/fft_module.cpp:24:76: note: in instantiation of function template specialization  
      'iterable_converter::construct<std::__1::vector<double, std::__1::allocator<double> > >' requested here  
                                                      &iterable_converter::construct<Container>,  
                                                                           ^  
src/fft_module.cpp:58:26: note: in instantiation of function template specialization  
      'iterable_converter::from_python<std::__1::vector<double, std::__1::allocator<double> > >' requested here  
    iterable_converter().from_python<std::vector<double>>();  
                         ^  
/usr/local/Cellar/boost/1.64.0_1/include/boost/python/object_core.hpp:331:27: note: template is declared here  
template <class T> struct extract;  
  
```  
  
this is a serious bug when extend python using c++ with vector input convertor

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-06-29 21:09:40 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312109391  

Would you please submit a (self-contained) test case, i.e. a C++ file, together with precise build instructions (including what compiler version you were using), to allow me to reproduce the issue ? Please try to make this code minimal, by removing any code not strictly necessary to reproduce the issue.  
Once I have that I will look into the issue and try to fix it, perhaps even in time for the upcoming (1.65) release. Thanks !

---

## Comment 2

> Username: yiakwy  
> Created at: 2017-06-30 05:46:57 UTC  
> Updated at: 2017-06-30 05:49:26 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312180904  

Thanks for fast response. We don't need test cases because it is a semantic error.  
  
To reproduce the error,   
  
System : MacOS 10.12  
Since python2.7 supports only .so, we use libc++ to compile to the target shared library  
compiler clang++, libc++:  
  
```shell  
Apple LLVM version 8.0.0 (clang-800.0.42.1)  
Target: x86_64-apple-darwin16.6.0  
Thread model: posix  
```  
  
Since boost157(compatible with NVIDA 8.0, xcode 7.3+, 8.0) needc libstdc++ and apple supports very an old version of libstdc++, I use combination of  
  
boost/1.64.0_1, (homebrew latest)  
boost-python/1.64.0  
libc++  
  
The error indicates that for stl_iterator library, extract are not implemented.

---

## Comment 3

> Username: yiakwy  
> Created at: 2017-06-30 05:48:55 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312181168  

Please refer to this project codes: https://github.com/yiakwy/yiakwy.github.io/blob/master/application/conv/src/fft_module.cpp  
  
Makefile:  
https://github.com/yiakwy/yiakwy.github.io/blob/master/application/conv/makefile

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-06-30 13:24:51 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312266187  

Please provide a minimal test case (a single source file without any dependencies, demonstrating the wrong or unexpected behaviour). That would improve the chances of me (or anyone else for that matter) being able to quickly review and fix any problem.  
(I believe your conclusion is wrong: the implementation of `boost::python::extract` is in no way related to stl_iterator, as it operates directly on Python objects. But to understand the real issue I really need a minimal test case.)

---

## Comment 5

> Username: yiakwy  
> Created at: 2017-07-01 05:41:38 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312412437  

Yes I totally understand. But it is about semantic error. This only happens  
when I include  
  
 #include <boost/python/suite/indexing/vector_indexing_suite.hpp>  
  
To best of my knowledge, providing makefile and describing environment  
perhaps is the best that I can do for you.  
  
2017-06-30 21:24 GMT+08:00 Stefan Seefeld <notifications@github.com>:  
  
> Please provide a minimal test case (a single source file without any  
> dependencies, demonstrating the wrong or unexpected behaviour). That would  
> improve the chances of me (or anyone else for that matter) being able to  
> quickly review and fix any problem.  
> (I believe your conclusion is wrong: the implementation of  
> boost::python::extract is in no way related to stl_iterator, as it  
> operates directly on Python objects. But to understand the real issue I  
> really need a minimal test case.)  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/137#issuecomment-312266187>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AIHdeNJmb-ThSk9r2tezLp3DDpLCH6vzks5sJPckgaJpZM4OJtpL>  
> .  
>

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-07-01 13:47:27 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312433109  

No, as I said, the best you can do is **reduce** the code to something **minimal**, so I don't have to download a full project repo and figure out how to build that. If the problem really is with the vector indexing suite, it should be fairly easy to come up with a minimal test case. (I'm not sure what you mean by "semantic error" here, and why that would imply not needing a test case.)

---

## Comment 7

> Username: yiakwy  
> Created at: 2017-07-04 05:15:24 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312782199  

The error happens when you do :  
  
// #include <boost/python/stl_iterator.hpp>  
  
in source file .cpp  
  
2017-07-01 21:47 GMT+08:00 Stefan Seefeld <notifications@github.com>:  
  
> No, as I said, the best you can do is *reduce* the code to something  
> *minimal*, so I don't have to download a full project repo and figure out  
> how to build that. If the problem really is with the vector indexing suite,  
> it should be fairly easy to come up with a minimal test case. (I'm not sure  
> what you mean by "semantic error" here, and why that would imply not  
> needing a test case.)  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/137#issuecomment-312433109>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AIHdeFNsc3HNn-29a7Qj0Jwx7h_8cpoNks5sJk3vgaJpZM4OJtpL>  
> .  
>

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2017-07-04 11:53:11 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-312858692  

That doesn't make any sense. Also, we do have a test for stl_iterator: https://github.com/boostorg/python/blob/develop/test/stl_iterator.cpp, which compiles just fine (https://travis-ci.org/boostorg/python/jobs/246900574#L1809-L1812).  
  
I reiterate another time: Please provide a **complete** but **minimal** **test** if you want anyone to be able to help you.

---

## Comment 9

> Username: yiakwy  
> Created at: 2017-07-05 11:34:10 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-313077807  

So the problem is that I can use boost except for stl_iterator. It makes sense because that error happens when <include> that header. Both errors reside in 1.57 and 1.64

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2017-07-20 12:21:28 UTC  
> Url: https://github.com/boostorg/python/issues/137#issuecomment-316686337  

Feel free to reopen the issue once you have a reproducible test case.
