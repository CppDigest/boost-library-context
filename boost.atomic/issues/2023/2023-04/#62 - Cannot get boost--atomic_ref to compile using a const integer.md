# #62 - Cannot get boost::atomic_ref to compile using a const integer [Closed]

> Username: chuchusoft  
> Created at: 2023-04-29 08:31:26 UTC  
> Updated at: 2023-04-29 12:21:36 UTC  
> Closed at: 2023-04-29 12:12:23 UTC  
> Url: https://github.com/boostorg/atomic/issues/62  

Hi,  
  
I'm trying to build the following code on Xcode 14.2 and boost 1.82.0 but I get a strange compilation error. Here's the code that's not compiling:  
  
```  
#include "boost/atomic.hpp"  
  
int main(int argc, const char * argv[]) {  
	const unsigned long		i=4;  
	unsigned long			j=6;  
  
	boost::atomic_ref<unsigned long>	ar0{j};  
	auto	val0{ar0.load(boost::memory_order::acquire)};  
	(void) val0;  
  
	boost::atomic_ref<const unsigned long>	ar1{i};  
	auto	val1{ar1.load(boost::memory_order::acquire)};  
	(void) val1;  
}  
```  
  
And here's the error that clang generates:  
  
```  
In file included from ../boost_1_82_0/boost/atomic.hpp:16:  
In file included from ../boost_1_82_0/boost/atomic/atomic_ref.hpp:24:  
../boost_1_82_0/boost/atomic/detail/atomic_ref_impl.hpp:99:17: error: reinterpret_cast from 'boost::atomics::detail::base_atomic_ref_common<const unsigned long, false, false>::value_type *' (aka 'const unsigned long *') to 'boost::atomics::detail::base_atomic_ref_common<const unsigned long, false, false>::storage_type *' (aka 'unsigned long long *') casts away qualifiers  
        return *reinterpret_cast< storage_type* >(m_value);  
                ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../boost_1_82_0/boost/atomic/detail/atomic_ref_impl.hpp:425:88: note: in instantiation of member function 'boost::atomics::detail::base_atomic_ref_common<const unsigned long, false, false>::storage' requested here  
        return atomics::detail::bitwise_cast< value_type >(core_operations::load(this->storage(), order));  
                                                                                       ^  
/Users/---/test_boost_atomic_ref/test_boost_atomic_ref/main.cpp:34:16: note: in instantiation of member function 'boost::atomics::detail::base_atomic_ref<const unsigned long, int, false>::load' requested here  
        auto    val1{ar1.load(boost::memory_order::acquire)};  
                         ^  
1 error generated.  
```  
  
In case it will help, here's the clang version:  
  
```  
% clang -v  
  
Apple clang version 14.0.0 (clang-1400.0.29.202)  
Target: arm64-apple-darwin21.6.0  
```  
  
Thanks for any help you can provide. Is this a bug in the boost/atomic library?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-29 12:13:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/62#issuecomment-1528774761  

Thanks for the report. Note that even `const`-qualified atomic objects must still reside in read-write memory.

---

## Comment 2

> Username: chuchusoft  
> Created at: 2023-04-29 12:21:36 UTC  
> Url: https://github.com/boostorg/atomic/issues/62#issuecomment-1528776164  

Thank you for fixing this.  
  
FYI, the code which generated this issue was from a const member function accessing a non-const member variable (which in the function's context becomes a const variable). Another member would atomically update the member variable, and this member would fetch/load it, so it's a real world problem.
