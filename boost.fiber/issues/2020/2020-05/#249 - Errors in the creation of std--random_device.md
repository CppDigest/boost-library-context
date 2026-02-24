# #249 - Errors in the creation of std::random_device [Open]

> Username: lenerd  
> Created at: 2020-05-30 17:02:36 UTC  
> Updated at: 2021-07-19 11:49:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/249  

Hi all,  
  
I noticed some of our tests crashing inside Boost.Fiber. More specific, the constructor of `std::random_device` throws an `std::runtime_error` with the description `random_device: rdseed failed` which is not handled.  
  
```  
#0  in __cxxabiv1::__cxa_throw (obj=obj@entry=0x7ffc580491f0, tinfo=0x5555559e3400 <typeinfo for std::runtime_error@@GLIBCXX_3.4>, dest=0x7ffff7770fd0 <std::runtime_error::~runtime_error()>) at /build/gcc/src/gcc/libstdc++-v3/libsupc++/eh_throw.cc:78  
#1  in std::__throw_runtime_error (__s=__s@entry=0x7ffff783f763 "random_device: rdseed failed") at /build/gcc/src/gcc/libstdc++-v3/src/c++11/functexcept.cc:102  
#2  in std::(anonymous namespace)::__x86_rdseed () at /build/gcc/src/gcc/libstdc++-v3/src/c++11/random.cc:108  
#3  in boost::fibers::detail::spinlock_ttas::lock() ()  
#4 ...  
```  
  
Inside of Boost.Fiber, `std::random_device`s are constructed to seed static, thread local random number generators in the spinlock implementations as well as in the work stealing scheduler.  
  
https://github.com/boostorg/fiber/blob/df4a190f5b92ba86395cf58b1040ed295caf9fc5/include/boost/fiber/detail/spinlock_ttas.hpp#L42  
https://github.com/boostorg/fiber/blob/df4a190f5b92ba86395cf58b1040ed295caf9fc5/src/algo/work_stealing.cpp#L68  
  
At these locations, it is assumed that the construction is successful. However, the constructor might throw an [implementation-defined exception](https://en.cppreference.com/w/cpp/numeric/random/random_device/random_device#Exceptions) if something goes wrong.  
  
In our case, such failures occurred using the `libstdc++` released with GCC 10.1.0 on machines which support the `rdseed` instruction. In a situation where multiple threads are spawned and need to initialized these random number generators, the available entropy exhausted and `rdseed` fails often enough for `std::random_device` to give up.  
  
This behavior was already reported in the [GCC bugtracker](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=94087) and a fallback to `rdrand` had been added for the next (?) GCC release. This change does not guarantee, that `std::random_device` is constructed successfully, but it makes it more likely. So it would be great if Boost.Fiber would handle possible errors.  
  
In the meantime, is there a good way for a user of Boost.Fiber to handle this error?

---

## Comment 1

> Username: ukr-precitec  
> Created at: 2021-07-19 08:48:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/249#issuecomment-882367268  

A similar problem can occur on Arm64-based systems when spinlock_ttas lock() accesses std::uniform_int_distribution and the resulting call to std::random_device::_M_getval() causes a run-time error.

---

## Comment 2

> Username: olk  
> Created at: 2021-07-19 11:18:30 UTC  
> Updated at: 2021-07-19 11:19:47 UTC  
> Url: https://github.com/boostorg/fiber/issues/249#issuecomment-882467300  

The error happens at runtime - how should boost.fiber handle exhaustion of entropy?  
Good random values require a certain degree of entropy.

---

## Comment 3

> Username: ukr-precitec  
> Created at: 2021-07-19 11:49:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/249#issuecomment-882484370  

> The error happens at runtime - how should boost.fiber handle exhaustion of entropy?  
> Good random values require a certain degree of entropy.  
  
The problem we're facing is not about entropy, but caused by the fact that std::uniform_int_distribution's call to std::random_device in spinlock_ttas's lock() routine can fail with an exception although it is declared "noexcept", hence causing the program to abort. Our workaround looks similar to the code below:  
```  
if ( spinlock_status::locked == state_.exchange( spinlock_status::locked, std::memory_order_acquire) ) {  
  
...  
  
std::size_t z = 0;  
try {                          
  std::uniform_int_distribution< std::size_t > distribution{  
                      0, static_cast< std::size_t >( 1) << (std::min)(collisions, static_cast< std::size_t >(  
BOOST_FIBERS_CONTENTION_WINDOW_THRESHOLD)) };  
    z = distribution( generator);  
}  
catch (...)  
{  
  z =  1;  
}  
```
