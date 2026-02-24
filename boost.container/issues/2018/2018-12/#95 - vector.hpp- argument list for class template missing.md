# #95 - vector.hpp: argument list for class template missing [Closed]

> Username: marcfehling  
> Created at: 2018-12-23 19:14:52 UTC  
> Updated at: 2018-12-31 16:23:33 UTC  
> Closed at: 2018-12-31 16:23:33 UTC  
> Url: https://github.com/boostorg/container/issues/95  

I am currently trying to include several boost headers which themselves rely on `boost/container/vector.hpp` in our code project, but compilation with the Intel compiler `Intel 19.0.0.117 20180804` leads to the following errors:  
```  
.../boost-1.69.0/include/boost/container/vector.hpp(3369): error: argument list for class template "boost::container::vector" is missing  
  vector(InputIterator, InputIterator) ->  
  ^  
  
.../boost-1.69.0/include/boost/container/vector.hpp(3369): error: expected a ")"  
  vector(InputIterator, InputIterator) ->  
                      ^  
  
.../boost-1.69.0/include/boost/container/vector.hpp(3369): error: expected a ";"  
  vector(InputIterator, InputIterator) ->  
                                       ^  
```  
---  
`boost 1.69.0` was built with:  
```  
bootstrap.sh --without-libraries= --with-toolset=intel-linux  
b2 toolset=intel-linux install  
```  
---  
I tried to reproduce this on older versions of both compiler and boost. `Intel 18` and `boost 1.66.0` seem to be compatible. Could you try to reproduce this?  
  
See https://github.com/dealii/dealii/issues/7546 for further information.

---

## Comment 1

> Username: marcfehling  
> Created at: 2018-12-23 19:16:38 UTC  
> Url: https://github.com/boostorg/container/issues/95#issuecomment-449658034  

This is the code in question:  
https://github.com/boostorg/container/blob/43c0257871e2f4a5d533c059f197a635a58361f2/include/boost/container/vector.hpp#L3369

---

## Comment 2

> Username: marcfehling  
> Created at: 2018-12-23 19:42:25 UTC  
> Url: https://github.com/boostorg/container/issues/95#issuecomment-449659376  

At first glance, it seems that `Intel 19` does not provide class template argument deduction (CTAD).  
  
Compilation with `Intel 18.0.2 20180210` and `boost 1.69.0` works flawlessly.

---

## Comment 3

> Username: marcfehling  
> Created at: 2018-12-23 20:39:21 UTC  
> Url: https://github.com/boostorg/container/issues/95#issuecomment-449661978  

The [documentation](https://software.intel.com/en-us/articles/c17-features-supported-by-intel-c-compiler) for the compiler says that CTAD has not been included in any of Intel's compilers yet (look for entry P0091R3).  
  
Thus we need a different statement to check whether CTAD is supported than the current one:  
https://github.com/boostorg/container/blob/43c0257871e2f4a5d533c059f197a635a58361f2/include/boost/container/detail/workaround.hpp#L123
