# #14 Error "call to 'endian_reverse' is ambiguous" [Closed]

> Username: zmij  
> Created at: 2015-07-22 15:19:32 UTC  
> Updated at: 2015-07-23 13:00:22 UTC  
> Closed at: 2015-07-23 12:16:22 UTC  
> Url: https://github.com/boostorg/endian/pull/14  

Compiler:  
  
```  
$ gcc -v  
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1  
Apple LLVM version 6.1.0 (clang-602.0.53) (based on LLVM 3.6.0svn)  
Target: x86_64-apple-darwin14.4.0  
Thread model: posix  
```  
  
The error:  
  
```  
In file included from <file_name.inl>: error: call to 'endian_reverse' is ambiguous  
    return endian_reverse(x);  
           ^~~~~~~~~~~~~~  
<file_name.inl>: note: in instantiation of function template specialization 'boost::endian::native_to_big<long>'  
      requested here  
        T tmp = boost::endian::native_to_big(base_type::value);// A long unwrapping from a call with long type  
  
/opt/local/include/boost/endian/conversion.hpp:197:17: note: candidate function  
  inline int8_t endian_reverse(int8_t x) BOOST_NOEXCEPT  
                ^  
/opt/local/include/boost/endian/conversion.hpp:202:18: note: candidate function  
  inline int16_t endian_reverse(int16_t x) BOOST_NOEXCEPT  
                 ^  
/opt/local/include/boost/endian/conversion.hpp:212:18: note: candidate function  
  inline int32_t endian_reverse(int32_t x) BOOST_NOEXCEPT  
                 ^  
/opt/local/include/boost/endian/conversion.hpp:225:18: note: candidate function  
  inline int64_t endian_reverse(int64_t x) BOOST_NOEXCEPT  
                 ^  
/opt/local/include/boost/endian/conversion.hpp:239:18: note: candidate function  
  inline uint8_t endian_reverse(uint8_t x) BOOST_NOEXCEPT  
                 ^  
/opt/local/include/boost/endian/conversion.hpp:244:19: note: candidate function  
  inline uint16_t endian_reverse(uint16_t x) BOOST_NOEXCEPT  
                  ^  
/opt/local/include/boost/endian/conversion.hpp:254:19: note: candidate function  
  inline uint32_t endian_reverse(uint32_t x) BOOST_NOEXCEPT  
                  ^  
/opt/local/include/boost/endian/conversion.hpp:267:19: note: candidate function  
  inline uint64_t endian_reverse(uint64_t x) BOOST_NOEXCEPT  
                  ^  
```  
  
The problem is that type long doesn't match int64_t exactly, so the compiler is unsure what overload to use. I propose to use a template structure that is selected based on the datatype's size and sign and doesn't have overloads to confuse the compiler.

---

## Comment 1

> Username: Beman  
> Created_at: 2015-07-23 12:16:21 UTC  
> Url: https://github.com/boostorg/endian/pull/14#issuecomment-124078552  

The behavior you describe is a feature, not a bug. It was a very deliberate design decision, documented in the FAQ:  
  
> **Why are exact-length 8, 16, 32, and 64-bit integers supported rather than the built-in char, short, int, long, long long, etc?**  
>   
> The primary use case, portable file or network data, needs these de facto standard sizes. Using types that vary with the platform would greatly limit portability for both programs and data.  
  
I'm curious about your use case. Why is `long` used rather than `int32_t` or `nt64_t`?  
  
In the meantime, I'm closing this pull request without applying it.  
  
Thanks,  
  
--Beman

---

## Comment 2

> Username: zmij  
> Created_at: 2015-07-23 12:52:33 UTC  
> Updated_at: 2015-07-23 13:00:22 UTC  
> Url: https://github.com/boostorg/endian/pull/14#issuecomment-124085229  

Actually, long is what I get from with clang compiler with the following typedef:  
  
``` c++  
// typedefs common header  
typedef boost::int_t<64>::exact     bigint;  
```  
  
As far as I work with network protocols I need exact datatype lengths, I don't rely on the sizes of int, long, long long etc.  
  
And the bug appears in the code where T = bigint.  
  
``` c++  
// i/o functions  
template <typename T>  
template <typename InputIterator >  
InputIterator  
binary_data_parser<T, INTEGRAL>::operator()(InputIterator begin, InputIterator end)  
{  
    typedef std::iterator_traits< InputIterator > iter_traits;  
    typedef typename iter_traits::value_type iter_value_type;  
    static_assert(std::is_same< iter_value_type, byte >::type::value,  
            "Input iterator must be over a char container");  
    assert( (end - begin) >= size() && "Buffer size is insufficient" );  
    value_type tmp(0);  
    char* p = reinterpret_cast<char*>(&tmp);  
    char* e = p + size();  
    while (p != e && begin != end) {  
        *p++ = *begin++;  
    }  
    tmp = boost::endian::big_to_native(tmp); // <<< HERE  
    std::swap( base_type::value, tmp );  
    return begin;  
}  
```  
  
With gcc compiler everything is fine, with clang - it goes crazy about the overloaded functions. When substituting the functions with template structures it selects correct conversion function.

---
