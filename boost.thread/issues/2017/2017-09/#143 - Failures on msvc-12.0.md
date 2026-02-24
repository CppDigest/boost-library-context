# #143 - Failures on msvc-12.0 [Closed]

> Username: pdimov  
> Created at: 2017-09-07 17:35:39 UTC  
> Updated at: 2018-04-13 07:02:07 UTC  
> Closed at: 2017-09-28 21:53:45 UTC  
> Url: https://github.com/boostorg/thread/issues/143  

Some tests are failing on msvc-12.0, see  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.3620  
  
```...failed compile-c-c++ bin.v2\libs\thread\test\queue_views__single_thread_p.test\msvc-12.0\debug\threading-multi\sync\mutual_exclusion\queue_views\single_thread_pass.obj...```

---

## Comment 1

> Username: viboes  
> Created at: 2017-09-07 20:16:30 UTC  
> Url: https://github.com/boostorg/thread/issues/143#issuecomment-327915653  

I don't know where the problem is. I've contacted Ion Gaztañaga in case he is able to see what is wrong.

---

## Comment 2

> Username: viboes  
> Created at: 2017-09-07 20:24:07 UTC  
> Url: https://github.com/boostorg/thread/issues/143#issuecomment-327917489  

I believe that I have an hint   
  
```  
  
        C:\projects\boost\boost/thread/csbl/devector.hpp(31) : see reference to function template instantiation 'void boost::container::vector<T,boost::container::new_allocator<T>>::push_back(const T &)' being compiled  
        with  
        [  
            T=non_copyable  
        ]  
```  
  
I suspect that `push_back(const T &)` should be defined using SFINAE :(

---

## Comment 3

> Username: viboes  
> Created at: 2017-09-07 20:28:02 UTC  
> Url: https://github.com/boostorg/thread/issues/143#issuecomment-327918425  

Wondering why do we see `const T&` when the code is  
  
```  
      template <class U>  
      void priv_push_back(BOOST_FWD_REF(U) x)  
      { data_.push_back(boost::forward<U>(x)); }  
```

---

## Comment 4

> Username: viboes  
> Created at: 2017-09-13 19:12:43 UTC  
> Url: https://github.com/boostorg/thread/issues/143#issuecomment-329267965  

Ion said me that he doesn't understand why the compiler is reporting this error.  
  
I've tried to setup an appveyor CI, but I don't reach to get the results.  
Once I will get the results I could create a branch trying to expand the Boost.Move macro and try to change some things in order to have a better idea.  
  
Any help here is welcome.  
  
For the time been I could inhibit these test while compiling on this compiler version. A PR is welcome if this behavior is desired.

---

## Comment 5

> Username: viboes  
> Created at: 2017-09-18 16:22:21 UTC  
> Url: https://github.com/boostorg/thread/issues/143#issuecomment-330276209  

I have masked the code that makes the compilation fail.  
  
https://github.com/boostorg/thread/pull/149  
  
See the results here  
  
https://ci.appveyor.com/project/viboes/thread/build/1.0.17-develop  
  
Any comment on the PR is welcome.
