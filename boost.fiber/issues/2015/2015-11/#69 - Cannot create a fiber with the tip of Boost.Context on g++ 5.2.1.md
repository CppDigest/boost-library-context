# #69 - Cannot create a fiber with the tip of Boost.Context on g++ 5.2.1 [Closed]

> Username: tgockel  
> Created at: 2015-11-17 00:34:11 UTC  
> Updated at: 2015-11-18 08:05:14 UTC  
> Closed at: 2015-11-17 05:48:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/69  

When trying to use Boost.Fiber with the tip of Boost.Context, I get a horrendous compilation error with basic use -- [attached](https://github.com/olk/boost-fiber/files/36112/boost.fiber-use-error.txt).  
  
```  
// Event the simplest case does not work:  
boost::fibers::fiber f([] () {});  
```  
  
It looks like this broke with this change in Boost.Context: [43d89cb6396706871ce9378c8e3b2c63e93ffe0e - support passing argument between execution_context](https://github.com/boostorg/context/commit/43d89cb6396706871ce9378c8e3b2c63e93ffe0e).  
  
The preceding commit works perfectly fine for me: [01660915c1330a186ed0548b436065a9fc8ec96c](https://github.com/boostorg/context/commit/01660915c1330a186ed0548b436065a9fc8ec96c)  
  
My compiler is g++ 5.2.1.

---

## Comment 1

> Username: olk  
> Created at: 2015-11-17 05:48:28 UTC  
> Updated at: 2015-11-17 05:50:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/69#issuecomment-157276808  

lambda requires argument: `[] (void*vp) {};`

---

## Comment 2

> Username: tgockel  
> Created at: 2015-11-17 21:52:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/69#issuecomment-157520810  

Clearly I'm missing something, because this code also fails to compile:  
  
```  
#include <boost/fiber/all.hpp>  
  
void foo()  
{  
    boost::fibers::fiber f([] (void*) {});  
    f.detach();  
}  
```  
  
As does this:  
  
```  
#include <boost/fiber/all.hpp>  
  
void bar()  
{  
    boost::fibers::packaged_task<void ()> task([] () {});  
    boost::fibers::fiber f(std::move(task));  
    f.detach();  
}  
```  
  
Keep in mind that this _works_ with the Boost.Context released with 1.59, but does not with the current tip of Boost.Context. It changed with Boost.Context changeset 43d89cb6396706871ce9378c8e3b2c63e93ffe0e. This does not seem intentionally broken.

---

## Comment 3

> Username: olk  
> Created at: 2015-11-18 08:05:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/69#issuecomment-157639239  

Keep in mind that boost.fiber is still in development  
you should use branch develop for boost.context and boost.fiber
