# #189 - Expiring weak_ptr from python references to shared_ptr [Open]

> Username: matpen  
> Created at: 2018-03-08 20:21:46 UTC  
> Updated at: 2021-01-22 20:00:28 UTC  
> Url: https://github.com/boostorg/python/issues/189  

Today I stumbled upon the issue described [here](https://stackoverflow.com/questions/8233252/boostpython-and-weak-ptr-stuff-disappearing), and since I could not find any related issues in the tracker, I thought to add one. That stackoverflow post is very complete, but I have experimenting myself for a while, so I am attaching my own [test](https://github.com/boostorg/python/files/1794626/ptr_test.zip) to this issue.  
  
There is only one minor difference to note between my test and that stackoverflow post: I used C++14 (gcc) with `weak_ptr` and `shared_ptr` from the standard library, as opposed to the boost equivalents.  
  
Apart from that, one curious thing I noticed is that, if I use boost 1.58 as installed from my Ubuntu repos, it _does_ work as expected; whereas, if I use the newer boost 1.66, it does not. That is, if we change the first line in the attached `build.sh` to the following:  
```  
# removed -I/opt/boost-1.66/include  
g++ -c -std=gnu++1y -fPIC -o main.o main.cpp -I/usr/include/python2.7  
```  
then the pointers do not expire. So the bug might have been fixed in the past, and could have somehow re-emerged.  
  
For completeness, here are my system specs. I am happy to share more info as required!  
```  
matteo@ubuntu:~$ cat /etc/lsb-release   
DISTRIB_ID=Ubuntu  
DISTRIB_RELEASE=16.04  
DISTRIB_CODENAME=xenial  
DISTRIB_DESCRIPTION="Ubuntu 16.04.4 LTS"  
  
matteo@ubuntu:~$ gcc --version  
gcc (Ubuntu 5.4.1-2ubuntu1~16.04) 5.4.1 20160904  
Copyright (C) 2015 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
matteo@ubuntu:~$ dpkg -s libboost-dev | grep 'Version'  
Version: 1.58.0.1ubuntu1  
  
matteo@ubuntu:~$ cat /opt/boost-1.66/include/boost/version.hpp | grep "BOOST_LIB_VERSION"  
//  BOOST_LIB_VERSION must be defined to be the same as BOOST_VERSION  
#define BOOST_LIB_VERSION "1_66"  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-03-08 20:31:19 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-371614775  

Thanks for the report !  
Would you be able to verify whether this commit may have introduced the bug: https://github.com/boostorg/python/commit/97e4b34a15978ca9d7c296da2de89b78bba4e0d5  
It's a few releases old, and stems from me trying to add support to `std::shared_ptr`.

---

## Comment 2

> Username: matpen  
> Created at: 2018-03-10 20:16:05 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-372062485  

Today I found the time to test that commit, and it seems like it is not the culprit.  
  
What I did was:  
1. Clone the boost super-project (as explained [here](https://github.com/boostorg/boost/wiki/Getting-Started#installing-boost));  
2. `cd boost/libs/python` and `git checkout 97e4b34`;  
3. Back to boost root folder and `./bootstrap.sh && ./b2 && ./b2 headers`;  
4. Test with the code attached to my original post, confirm that the issue is there;  
5. `cd boost/libs/python` and `git checkout 97e4b34^`;  
6. Rebuild and test in the same way as 3. and 4.: the issue is still there.

---

## Comment 3

> Username: lucas-bremond  
> Created at: 2018-08-21 00:14:37 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-414505436  

Same issue here! Any idea?

---

## Comment 4

> Username: sigbjorn  
> Created at: 2018-11-04 18:56:55 UTC  
> Updated at: 2018-11-04 19:01:59 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-435695837  

Also experience this issue, with std or boost pointer, on windows platform as well as linux-platforms.  
c++11 .. c++-17  
  
With a similar  simple setup.  
  
In principle, exposing something like this via boost python, will break the weak_ptr immediately.  
(it's always expired after leaving the c++ scope, like the shared ptr passed to the routine is a copy of the 'real' shared_ptr image, as mentioned in the above logs. The only workaround, is to let B inherit from shared_from_this, and always use b->shared_from_this() instead of b wherever needed).  
```  
struct B; //etc.  
struct A {   
   A(shared_ptr<B> b):b_(b){} // b is expired after returning to python  
   weak_ptr<B> b_;  
   shared_ptr<B> b() {return b_.lock();} // expose this as property to python  
};  
  
struct B {  
   B()=default;  
   shared_ptr<A> a;  
}  
//-- in python  
b=B()  
a=A(b)  
b.a=a  
bx=a.b # you will always get None here, b is expired.   
```  
A similar setup with pybind11 do work, so it should be possible to fix, by inspecting the python converters more carefully and also include tests that use a similar setup.  
  
Would be nice to solve this issue,  since using weak-ptr's is one way of breaking ref-count loops, that is particularly useful if you also expose complex data-structures to python.  
If not exposing to python, using plain pointers etc. like suggested by Herb Sutter, works nice as well.

---

## Comment 5

> Username: duburcqa  
> Created at: 2021-01-19 21:21:57 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-763149484  

I can confirm this is still not working. I assume there is no plan to fix it ?

---

## Comment 6

> Username: matpen  
> Created at: 2021-01-19 21:31:36 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-763154467  

Still affects me too. However I can confirm that [the workaround](https://github.com/boostorg/python/issues/189#issuecomment-435695837) does work, provided of course that you can use it in your current situation (multiple inheritance, diamond problem, etc).

---

## Comment 7

> Username: duburcqa  
> Created at: 2021-01-19 21:53:05 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-763165552  

Yes, this workaround does work, but I don't like the idea of having to modify the original C++ code, which is also intended to be used without Python in my case,  because of Python bindings not working as expected otherwise.

---

## Comment 8

> Username: sigbjorn  
> Created at: 2021-01-22 20:00:28 UTC  
> Url: https://github.com/boostorg/python/issues/189#issuecomment-765651772  

Hi,  
  
This still affects us, ref.  shyft open-source framework for the energy-market, gitlab.com/shyft-os/shyft.  
  
We use boost python extensively, and the lack of proper handling of the shared_ptr/weak_ptr, force use to use a workaround specializing the boost shared_ptr handling for std::shared_ptr, and then fallback to the original solution (which is to construct a special shared_ptr with a custom-deleter ref'ing to the py-object instead of the real shared-ptr count block, - that directly violates whatever use you  would have of that particular shared-ptr).  
  
[shyft open source workaround for std::shared_ptr and boost](https://gitlab.com/shyft-os/shyft/-/blob/master/cpp/shyft/py/api/boostpython_pch.h#L103)  
  
   
As I understand the original code(I might be wrong here, so please bear over with me), the boost handling of shared-ptr (boost::shared_ptr at that time),  was a trick to preserve the  python id concept for the python exposed objects.  
  
E.g. in this context,   
id(some_cpp_exposed_object) == constant for the lifetime of  "some_cpp_exposed_object", or at least best effort..  
  
The workaround we have basically tries to see if the python object indeed  represents a c++ object with a shared_ptr as  holder object, and if so, use that. Thus, you get the (holding)  shared-ptr, valid as pr. usual, that works as expected regarding weak-ptr etc.  
  
It's worth to note that our problem started the day boost python implemented the std::shared_ptr handling (and doing the shared-ptr aliasing stuff to py-object etc..).  
  
The data-structures we have are shared through python, and shared_ptr felt like a natural choice, given the alternative that could be using  return-value policies more extensively (requires a bit more care when writing the py-api).  
 Weak ptr, are needed to break the cyclic dependencies in those topologies. Thus, everything did breake the moment the shared_ptr handling in boost was extended to give the std::shared_ptr the same handling as boost  shared_ptr.  
  
Prior to that we handled this our self, registering pointer to object converters etc, working  as expected.  
  
I think it could be worth considering for boost python, to put a bit more effort into this part of  the code.  
  
Our workaround, if put in place as part of the boost python,  will break some of the extensive test-suites around identity and shared-ptr handling.  (I have considered making a merge-request for this, but it seems to be difficult because the my  solution would break tests that are already in place, at least as it stands now).  
  
For me, it seems that the python **id(object)** of a c++ exposed object **can not** be guaranteed to be the same for the life-time of the **c++ object**, only for the returned **python-wrapper object**.  
  
The implication is that if you call two times a function to get hold of the same underlying c++ object, you should not expect to get the same python.id (unless you override the python.id, and return the pointer address of the c++ object..).  
  
If anyone have otherways of fixing this, that keeps current tests green, and in addition works nice with shared-ptr/weak-ptr (as indicated in the beginning of this thread), - I would be happy to contribute so that we can improve this  part of the library.
