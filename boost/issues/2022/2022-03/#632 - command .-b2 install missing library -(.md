# #632 - command ./b2 install missing library :( [Closed]

> Username: gesangtome  
> Created at: 2022-03-05 09:56:00 UTC  
> Updated at: 2022-03-25 09:57:47 UTC  
> Closed at: 2022-03-25 09:57:47 UTC  
> Url: https://github.com/boostorg/boost/issues/632  

Build platfrom: fedora 35  
source: boost 1.78.0  
  
Here is the RPMSPEC I am using: [boost.spec.zip](https://github.com/boostorg/boost/files/8190404/boost.spec.zip)  
  
No errors occurred during compilation, But the following libraries are not installed where I want.  
libboost_stacktrace_basic.so.1.78.0 and libboost_fiber.so.1.78.0.

---

## Comment 1

> Username: rodgert  
> Created at: 2022-03-08 20:14:38 UTC  
> Updated at: 2022-03-08 20:15:05 UTC  
> Url: https://github.com/boostorg/boost/issues/632#issuecomment-1062168410  

I am currently working on updating the boost package for [Fedora 37](https://fedoraproject.org/wiki/Changes/F37Boost178). If I just pull the release tarballs of boost_1_77_0 and boost_1_78_0 and build each outside of the fedpkg tooling, boost_1_77_0/stage/lib has the libraries but boost_1_78_0 does not.

---

## Comment 2

> Username: rodgert  
> Created at: 2022-03-09 01:11:21 UTC  
> Url: https://github.com/boostorg/boost/issues/632#issuecomment-1062450962  

The issue is  not unique to Fedora, happens on Ubuntu 21.10 with a 'plain' ./bootstrap.sh && ./b2 of boost_1_78_0

---

## Comment 3

> Username: mclow  
> Created at: 2022-03-09 01:15:53 UTC  
> Url: https://github.com/boostorg/boost/issues/632#issuecomment-1062453384  

See https://github.com/bfgroup/b2/issues/104

---

## Comment 4

> Username: rodgert  
> Created at: 2022-03-09 01:41:49 UTC  
> Url: https://github.com/boostorg/boost/issues/632#issuecomment-1062467977  

Applying https://github.com/bfgroup/b2/commit/78fd284a42caabe8815cb0870b46e5567872e75b.patch addresses the problem, will add the patch to the Fedora boost.spec.  
  
@mclow Thanks for the pointer!

---

## Comment 5

> Username: gesangtome  
> Created at: 2022-03-24 17:15:04 UTC  
> Updated at: 2022-03-25 09:57:22 UTC  
> Url: https://github.com/boostorg/boost/issues/632#issuecomment-1077853446  

@rodgert Very usefu, thank you   
@[mclow](https://github.com/mclow)
