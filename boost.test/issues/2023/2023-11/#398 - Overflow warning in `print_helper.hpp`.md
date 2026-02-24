# #398 - Overflow warning in `print_helper.hpp` [Closed]

> Username: JBouwer  
> Created at: 2023-11-13 23:35:20 UTC  
> Updated at: 2024-01-09 08:59:26 UTC  
> Closed at: 2024-01-09 08:59:26 UTC  
> Url: https://github.com/boostorg/test/issues/398  

Using:  
``  
clang++   -stdlib=libc++ -std=c++17 -Wall ...  
``  
on an Intel x64 Mac (Clang 11), with selected code [^1]  
results in:  
```  
...  
In file included from boost/test/unit_test.hpp:18:  
In file included from boost/test/test_tools.hpp:42:  
In file included from boost/test/tools/context.hpp:19:  
In file included from boost/test/utils/lazy_ostream.hpp:16:  
boost/test/tools/detail/print_helper.hpp:100:71: warning: overflow in expression; result is 2147483347 with type 'int' [-Winteger-overflow]  
            return ostr.precision( 2 + std::numeric_limits<T>::digits * 301/1000 );  
                                                                      ^  
```  
  
It is pretty easily fixed by promoting the calculation to the parameter type;  
``  
            return ostr.precision( 2 + std::streamsize(std::numeric_limits<T>::digits) * 301/1000 );  
``  
  
 [^1]: Occurences in my code are pretty obscure:  
For me it manifests during (implicit) printing of big enough types;  
I can induce it with:  
``  
    BOOST_CHECK_NE( boost::multiprecision::checked_cpp_int(std::numeric_limits<int>::max()), 0 );  
``
