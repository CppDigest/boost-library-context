# #167 - Gdb 'catch throw' and boost::fibers::broken_promise when moving promises [Closed]

> Username: savinz  
> Created at: 2018-01-09 21:10:37 UTC  
> Updated at: 2018-01-11 17:56:30 UTC  
> Closed at: 2018-01-11 07:20:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/167  

I am seeing boost::fibers::broken_promise exceptions with gdb `catch throw` when  
moving (std::move) boost::fibers::promise. This is due `std::make_exception_ptr` that gets  
called when tmp object goes out of scope in the move assignment operator:   
https://github.com/boostorg/fiber/blob/e325cf78d5c89497c89b4b9a01b94f3e56cbc9da/include/boost/fiber/future/promise.hpp#L70-L76   
  
Wouldn't be better to implement the move assignment the same way as the move constructor?  
  
Test cases (test1 - gdb `catch throw` shows broken_promise, test2 - no throws):  
```c++  
#include <boost/fiber/all.hpp>  
#include <iostream>  
void test1() {  
    boost::fibers::promise<int> p1, p2;  
    auto f = p1.get_future();  
    p2 = std::move(p1);  
    p2.set_value(1);  
    std::cout << f.get() << std::endl;  
}  
void test2() {  
    boost::fibers::promise<int> p1;  
    auto f = p1.get_future();  
    auto p2{ std::move(p1) };  
    p2.set_value(1);  
    std::cout << f.get() << std::endl;  
}  
```

---

## Comment 1

> Username: olk  
> Created at: 2018-01-10 05:18:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356502551  

promise implements move assignmentin terms of move constructor  
test1() and test2() do not throw at my system (gcc-7.2 + clang-5.0.1/Linux) (even in gdb with catch throw)

---

## Comment 2

> Username: savinz  
> Created at: 2018-01-10 08:01:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356527990  

Thanks, just tested with gcc-7.2 and looks like it does a way better job at optimizing the promise move  
assignment operator than gcc-6.3.0. Gdb catch throw shows no throws. I did a quick benchmark:  
  
gcc-6.3.0 (-O2)  
```  
-----------------------------------------------  
Benchmark        Time           CPU Iterations  
-----------------------------------------------  
test1         1604 ns       1605 ns     435152  
test2          111 ns        111 ns    6224944  
```  
gcc-7.2.0 (-O2)  
```  
-----------------------------------------------  
Benchmark        Time           CPU Iterations  
-----------------------------------------------  
test1          332 ns        333 ns    2097035  
test2          112 ns        112 ns    6193937  
```  
  
gcc-7.2.0 (-O2)  
changed promise assignment to use future_ = std::move( other.future_) instead of swap  
```  
-----------------------------------------------  
Benchmark        Time           CPU Iterations  
-----------------------------------------------  
test1          159 ns        159 ns    4370213  
test2          111 ns        112 ns    6237259  
```

---

## Comment 3

> Username: olk  
> Created at: 2018-01-10 13:24:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356601153  

which version do you use?  
` promise_base & operator=( promise_base && other)` is implemented with `promise_base( promise_base && other)` and the move contructor uses `future_{ std::move( other.future_) }`.  
  
what happend with the exceptions you have reported?

---

## Comment 4

> Username: savinz  
> Created at: 2018-01-10 14:29:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356618136  

I use the latest develop branch. The exceptions are really only visible with gdb catch throw due to  
how the std::make_exception_ptr is implemented in gcc 6 - with try catch. No exceptions are  
propagated into user program - sorry for not being clear. I stumbled on this while debugging  
exceptions in my program. It seemed a bit costly beside making noise with gdb. Gcc 7 has a  
different implementation of std::make_exception_ptr, it's faster and doesn't throw, so gdb doesn't  
show it. Still `promise_base & operator=( promise_base && other)` implemented with tmp object  
and swap results in `future_->owner_destroyed()` ... `std::make_exception_ptr` when tmp goes out  
of scope. This is quite a performance difference - at least with gcc 6. In my program I am seeing a  
12% improvement in speed if I change the move assignment operator to:  
```c++  
    promise_base & operator=( promise_base && other) noexcept {  
        if ( BOOST_LIKELY( this != & other) ) {                                               
            obtained_ = other.obtained_;   
            future_ = std::move( other.future_);  
            other.obtained_ = false;   
        }  
        return * this;  
    }  
```  
Or if I just avoid using it altogether and stick with moving promises using move constructor only.  
Gcc 7 produces much faster code, but still it's a factor of 2x between move constructor and operator.

---

## Comment 5

> Username: olk  
> Created at: 2018-01-11 07:20:28 UTC  
> Updated at: 2018-01-11 14:07:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356847622  

the move assignment operator is  mssing `future_->owner_destroyed();`  
  
```  
promise_base & operator=( promise_base && other) noexcept {  
        if ( BOOST_LIKELY( this != & other) ) {                                               
            obtained_ = other.obtained_;   
            if ( future_) future_->owner_destroyed();  
            future_ = std::move( other.future_);  
            other.obtained_ = false;   
        }  
        return * this;  
    }  
```  
  
Otherwise a future constructed from a promise that gets move-assigned another shared-state will wait forever in `future<>::get()` or `future<>::wait()`.  
  
Suppose following example:  
```  
boost::fibers::promise<int> p1, p2;  
auto f1 = p1.get_future();  
auto f2 = p2.get_future();  
p2 = std::move(p1);  
p2.set_value(1);  
std::cout << f1.get() << std::endl;  
std::cout << f2.get() << std::endl;  
```  
`f2.get()` throws int the currrent version:  
'_The associated promise has been destructed prior to the associated state becoming ready._'

---

## Comment 6

> Username: savinz  
> Created at: 2018-01-11 12:18:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356918051  

Ah, yes ... thanks for pointing this out.  
  
I see there is the  `obtained_` flag and the promise_base  `get_future` already has guards  
`if (obtained_)...` and `if (!future_ )...`, so the  `future_->owner_destroyed()` is only needed  
in move assignment (and in the promise_base destructor) if `obtained_` is true. Or am I  
missing something?

---

## Comment 7

> Username: olk  
> Created at: 2018-01-11 14:12:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356945258  

`future_->owner_destroyed()` is required if a shared-state is available  
`obtained` is only need to prevent to generate a second future

---

## Comment 8

> Username: savinz  
> Created at: 2018-01-11 15:08:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356962036  

I see `future_->owner_destroyed()` sets the broken_promise() exception if it has not been  
marked ready. Since the future_ is created in promise_base constructor the `owner_destroyed`  
will be called regardless of whether get_future() was called or not (the future_ is actually used -  
shared). So if I do this:  
```c++  
    boost::fibers::promise<int> p1, p2;  
    auto f = p1.get_future();  
    p2 = std::move(p1);  
```  
I face the overhead of temporary future_ object and more importantly std::make_exception_ptr  
which is significant under gcc 6 (I yet check clang or msvc).  The move constructor doesn't  
have this overhead, this is really what I wanted to point out.

---

## Comment 9

> Username: olk  
> Created at: 2018-01-11 17:05:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-356994103  

Maybe a modification of ~promise_base() could help:  
  
```  
    ~promise_base() {  
        if ( future_ && obtained_) {  
            future_->owner_destroyed();  
        }  
    }  
```  
`shared_state::owner_destroyed()` is called when a shared-state was initialized and a future<> was created from it. could yout test it/measure the timing?

---

## Comment 10

> Username: savinz  
> Created at: 2018-01-11 17:32:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-357002021  

gcc-6.3.0 (-O2)  
```  
-----------------------------------------------  
Benchmark        Time           CPU Iterations  
-----------------------------------------------  
test1          160 ns        160 ns    4362747  
test2          111 ns        111 ns    6207349  
```  
gcc-7.2.0 (-O2)  
```  
-----------------------------------------------  
Benchmark        Time           CPU Iterations  
-----------------------------------------------  
test1          158 ns        158 ns    4396806  
test2          112 ns        112 ns    6195495  
```  
With this change promise move assignment is ~10x faster with gcc 6 and ~2x faster with gcc 7 :-)

---

## Comment 11

> Username: olk  
> Created at: 2018-01-11 17:56:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/167#issuecomment-357008824  

commit pushed, ty
