# #673 - Compile failure in bernoulli_details.hpp when BOOST_MATH_NO_ATOMIC_INT [Closed]

> Username: heinemml  
> Created at: 2021-08-17 19:53:32 UTC  
> Updated at: 2021-09-06 15:57:55 UTC  
> Closed at: 2021-09-06 15:57:55 UTC  
> Url: https://github.com/boostorg/math/issues/673  

I'm currently integrating boost 1.77 into buildroot.  
  
The boost-math build is now failing for the NIOSII Platform.  
The Platform does not support atomic ints. So `BOOST_MATH_NO_ATOMIC_INT` is defined in `tools/atomic.hpp`.  
  
Since 1.77 this yields the following build error:  
```  
In file included from ./boost/math/special_functions/bernoulli.hpp:16,  
                 from ./boost/math/special_functions/gamma.hpp:35,  
                 from ./boost/math/special_functions/detail/bessel_jy.hpp:14,  
                 from ./boost/math/special_functions/bessel.hpp:20,  
                 from ./boost/math/special_functions/airy.hpp:12,  
                 from ./boost/math/special_functions.hpp:15,  
                 from libs/math/build/../src/tr1/pch.hpp:9:  
./boost/math/special_functions/detail/bernoulli_details.hpp:560:4: error: 'atomic_counter_type' does not name a type  
  560 |    atomic_counter_type m_counter, m_current_precision;  
      |    ^~~~~~~~~~~~~~~~~~~  
./boost/math/special_functions/detail/bernoulli_details.hpp: In constructor 'boost::math::detail::bernoulli_numbers_cache<T, Policy>::bernoulli_numbers_cache()':  
./boost/math/special_functions/detail/bernoulli_details.hpp:212:9: error: class 'boost::math::detail::bernoulli_numbers_cache<T, Policy>' does not have any field named 'm_counter'  
  212 |       , m_counter(0)  
      |         ^~~~~~~~~  
./boost/math/special_functions/detail/bernoulli_details.hpp:213:9: error: class 'boost::math::detail::bernoulli_numbers_cache<T, Policy>' does not have any field named 'm_current_precision'  
  213 |       , m_current_precision(boost::math::tools::digits<T>())  
      |         ^~~~~~~~~~~~~~~~~~~  
./boost/math/special_functions/detail/bernoulli_details.hpp: In member function 'OutputIterator boost::math::detail::bernoulli_numbers_cache<T, Policy>::copy_bernoulli_numbers(OutputIterator, std::size_t, std::size_t, const Policy&)':  
./boost/math/special_functions/detail/bernoulli_details.hpp:392:36: error: 'm_counter' was not declared in this scope  
  392 |       if((static_cast<std::size_t>(m_counter.load(std::memory_order_consume)) < start + n)  
      |                                    ^~~~~~~~~  
./boost/math/special_functions/detail/bernoulli_details.hpp:393:31: error: 'm_current_precision' was not declared in this scope; did you mean 'double_precision'?  
  393 |          || (static_cast<int>(m_current_precision.load(std::memory_order_consume)) < boost::math::tools::digits<T>()))  
      |                               ^~~~~~~~~~~~~~~~~~~  
      |                               double_precision  
./boost/math/special_functions/detail/bernoulli_details.hpp:413:41: error: 'atomic_integer_type' does not name a type  
  413 |             m_counter.store(static_cast<atomic_integer_type>(bn.size()), std::memory_order_release);  
      |                                         ^~~~~~~~~~~~~~~~~~~  
./boost/math/special_functions/detail/bernoulli_details.hpp: In member function 'OutputIterator boost::math::detail::bernoulli_numbers_cache<T, Policy>::copy_tangent_numbers(OutputIterator, std::size_t, std::size_t, const Policy&)':  
./boost/math/special_functions/detail/bernoulli_details.hpp:504:36: error: 'm_counter' was not declared in this scope  
  504 |       if((static_cast<std::size_t>(m_counter.load(std::memory_order_consume)) < start + n)  
      |                                    ^~~~~~~~~  
./boost/math/special_functions/detail/bernoulli_details.hpp:505:31: error: 'm_current_precision' was not declared in this scope; did you mean 'double_precision'?  
  505 |          || (static_cast<int>(m_current_precision.load(std::memory_order_consume)) < boost::math::tools::digits<T>()))  
      |                               ^~~~~~~~~~~~~~~~~~~  
      |                               double_precision  
./boost/math/special_functions/detail/bernoulli_details.hpp:525:41: error: 'atomic_integer_type' does not name a type  
  525 |             m_counter.store(static_cast<atomic_integer_type>(bn.size()), std::memory_order_release);  
      |                                         ^~~~~~~~~~~~~~~~~~~  
```  
  
Since 1.77 `special_functions/detail/bernoulli_details.hpp` has no special handling for `BOOST_MATH_NO_ATOMIC_INT` anymore. Is this intentional or an oversight?  
  
For now I would disable the math build for NIOSII Platform.

---

## Comment 1

> Username: mborland  
> Created at: 2021-08-22 17:00:58 UTC  
> Url: https://github.com/boostorg/math/issues/673#issuecomment-903298918  

Does the NIOSII platform offer any of the other threading headers (e.g. `<mutex>`, `<thread>`, etc.), or is this strictly limited to no hardware implementation of atomics?

---

## Comment 2

> Username: heinemml  
> Created at: 2021-08-26 19:21:49 UTC  
> Url: https://github.com/boostorg/math/issues/673#issuecomment-906678808  

Dislaimer: I'm not really involved into the nios2 toolchain. I just check the packages against the different buildroot support platforms when updating.  
  
The nios2 toolchain currently used by buildroot is this one: https://toolchains.bootlin.com/downloads/releases/toolchains/nios2/tarballs/nios2--glibc--stable-2020.08-1.tar.bz2  
The threading headers are all there.  
  
I think the issue is similar to one we had with boost-atomic (https://github.com/boostorg/atomic/issues/42) since 1.74.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-09-01 17:50:36 UTC  
> Url: https://github.com/boostorg/math/issues/673#issuecomment-910514977  

Confirmed as an issue.  
  
This will continue to be unsupported, but we should fail much more gracefully so that only the small number of use cases that are unsupported cause issues.  I have a few thoughts about that.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-09-01 19:33:48 UTC  
> Url: https://github.com/boostorg/math/issues/673#issuecomment-910634116  

I wonder if I can I get you to verify that https://github.com/boostorg/math/pull/684 fixes this your end?

---

## Comment 5

> Username: heinemml  
> Created at: 2021-09-04 20:28:50 UTC  
> Url: https://github.com/boostorg/math/issues/673#issuecomment-913036353  

With this patchset applied I can compile boost-math for niosII again.  
  
So the Bernoulli code is now not available anymore since 1.77.0 on platforms with no atomic ints right?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-09-05 08:39:51 UTC  
> Url: https://github.com/boostorg/math/issues/673#issuecomment-913110916  

It *is* available for float/double/long double, or *without thread safety* for user-defined (multiprecision) types.
