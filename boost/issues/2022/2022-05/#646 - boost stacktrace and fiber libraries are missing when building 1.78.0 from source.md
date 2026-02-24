# #646 - boost stacktrace and fiber libraries are missing when building 1.78.0 from source [Closed]

> Username: nknadim1  
> Created at: 2022-05-04 09:15:58 UTC  
> Updated at: 2022-07-09 21:07:41 UTC  
> Closed at: 2022-07-09 21:07:41 UTC  
> Url: https://github.com/boostorg/boost/issues/646  

Hi,  
  
I am trying to build Boost 1.78.0 from source in Linux but have noticed that the stacktrace and fiber libraries are missing. I am using the following command to build boost.  
bootstrap.sh  
b2 -j8 --build-dir=/opt/disk1/boost/boost_build --prefix=/opt/disk1/boost_install/ --without-python address-model=64 toolset=gcc threading=multi variant=release install  
  
I couldn't see any error in the log as well. So other libraries apart from these are building fine. I am using GCC 7.2.0 compiler, I tried with intel 2022 compiler as well but no luck.  
  
Please suggest if I am missing something here.  
  
Thanks.

---

## Comment 1

> Username: mclow  
> Created at: 2022-05-04 13:26:28 UTC  
> Url: https://github.com/boostorg/boost/issues/646#issuecomment-1117312849  

Im pretty sure that this was fixed in 1.79.0. See https://github.com/boostorg/stacktrace/issues/119

---

## Comment 2

> Username: mclow  
> Created at: 2022-05-04 13:28:46 UTC  
> Url: https://github.com/boostorg/boost/issues/646#issuecomment-1117315329  

Even better: https://github.com/bfgroup/b2/issues/104

---

## Comment 3

> Username: nknadim1  
> Created at: 2022-05-04 16:23:01 UTC  
> Url: https://github.com/boostorg/boost/issues/646#issuecomment-1117553057  

Ok, I will try 1.79.0  
  
Thanks.
