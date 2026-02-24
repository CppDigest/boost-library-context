# #313 - Compile time error with intel 19.0.4.243 [Open]

> Username: aminiussi  
> Created at: 2020-07-15 10:19:04 UTC  
> Updated at: 2020-07-15 11:48:02 UTC  
> Url: https://github.com/boostorg/python/issues/313  

I suspect the compiler is at fault, but still:  
```  
intel-linux.compile.c++ bin.v2/libs/python/build/intel-linux/release/python-3.6/threading-multi/visibility-hidden/converter/registry.o  
./boost/operators.hpp(169): error #3377: constexpr function return is non-constant  
       friend BOOST_OPERATORS_CONSTEXPR bool operator!=(const T& x, const T& y) { return !static_cast<bool>(x == y); }  
                                                                                         ^  
          detected during instantiation of class "boost::operators_impl::equality_comparable1<T, B> [with T=boost::python::type_info, B=boost::operators_impl::operators_detail::empty_base<boost::python::type_  
info>]" at line 302 of "libs/python/src/converter/registry.cpp"  
  
compilation aborted for libs/python/src/converter/registry.cpp (code 2)  
  
    "icpc" -c -xc++ -fvisibility-inlines-hidden -fPIC -m64 -pthread -fvisibility=hidden -w1 -inline-level=2 -O3 -ip -std=c++11 -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG -I"." -I"/opt/software/occige  
n/tools/intelpython3/2019.3/intelpython3/include/python3.6m"   -c -o "bin.v2/libs/python/build/intel-linux/release/python-3.6/threading-multi/visibility-hidden/converter/registry.o" "libs/python/src/converter  
/registry.cpp"  
  
...failed intel-linux.compile.c++ bin.v2/libs/python/build/intel-linux/release/python-3.6/threading-multi/visibility-hidden/converter/registry.o...  
```  
Thanks

---

## Comment 1

> Username: aminiussi  
> Created at: 2020-07-15 11:48:02 UTC  
> Url: https://github.com/boostorg/python/issues/313#issuecomment-658721657  

Note that it compile with c++14 (vs 11) mode enabled, but I'm not sure if this is intended.
