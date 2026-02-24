# #219 Commented out unused argument name (stack_decorator::apply) [Closed]

> Username: kedarbhat  
> Created at: 2019-04-21 19:32:39 UTC  
> Updated at: 2019-08-05 19:04:43 UTC  
> Closed at: 2019-08-05 19:04:43 UTC  
> Url: https://github.com/boostorg/test/pull/219  

Both GCC and Clang complain about the argument to `stack_decorator::apply` being unused; commenting out the variable name quiets the warning

---

## Comment 1

> Username: jeking3  
> Created_at: 2019-06-07 09:58:24 UTC  
> Url: https://github.com/boostorg/test/pull/219#issuecomment-499828519  

```  
jking@ubuntu:~/boost/libs/dynamic_bitset/test$ bdde  
docker run --cap-add=SYS_PTRACE --security-opt seccomp=unconfined -v /home/jking/boost:/boost:rw -v /home/jking/bdde:/bdde:ro -v /home/jking/.vimrc:/home/boost/.vimrc:ro --workdir /boost/libs/dynamic_bitset/test -it jeking3/bdde:linux /bin/bash  
boost@e83a30c3971b:/boost/libs/dynamic_bitset/test$ b2 dyn_bitset_unit_tests1 cxxflags=-Wextra cxxflags=-pedantic  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...found 2182 targets...  
...updating 4 targets...  
gcc.compile.c++ ../../../bin.v2/libs/dynamic_bitset/test/dyn_bitset_unit_tests1.test/gcc-7/debug/threading-multi/visibility-hidden/dyn_bitset_unit_tests1.o  
In file included from ../../../boost/test/included/unit_test.hpp:21:0,  
                 from bitset_test.hpp:31,  
                 from dyn_bitset_unit_tests1.cpp:18:  
../../../boost/test/impl/decorator.ipp: In member function 'virtual void boost::unit_test::decorator::stack_decorator::apply(boost::unit_test::test_unit&)':  
../../../boost/test/impl/decorator.ipp:114:36: warning: unused parameter 'tu' [-Wunused-parameter]  
 stack_decorator::apply( test_unit& tu )  
                                    ^~  
```

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2019-07-21 13:44:49 UTC  
> Url: https://github.com/boostorg/test/pull/219#issuecomment-513555856  

Looks good, in `next`. Thanks!

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2019-08-05 19:04:43 UTC  
> Url: https://github.com/boostorg/test/pull/219#issuecomment-518358986  

It is in master now and should be part of 1.71 release, thanks!

---
