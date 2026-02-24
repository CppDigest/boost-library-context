# #44 Fix BOOST_STATIC_ASSERT when passing enums (regression) [Closed]

> Username: Chaosvex  
> Created at: 2020-05-02 01:09:03 UTC  
> Updated at: 2020-05-03 14:16:59 UTC  
> Closed at: 2020-05-03 14:16:59 UTC  
> Url: https://github.com/boostorg/endian/pull/44  

Boost 1.71.0 introduced a regression that breaks the ability to reverse enum types.  
  
For example:  
```cpp  
#include <boost/endian/conversion.hpp>  
  
enum class Foo { BAR };  
  
int main() {  
    Foo bar { Foo::BAR };  
    boost::endian::native_to_little_inplace(bar);  
}  
```  
  
Under 1.70, this compiles but under 1.71:  
```  
In file included from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/endian/detail/endian_reverse.hpp:13,  
                 from /opt/wandbox/boost-1.71.0/gcc-head/include/boost/endian/conversion.hpp:11,  
                 from prog.cc:1:  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/endian/conversion.hpp: In instantiation of 'void boost::endian::native_to_little_inplace(EndianReversibleInplace&) [with EndianReversibleInplace = Foo]':  
prog.cc:7:48:   required from here  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/endian/conversion.hpp:330:89: error: static assertion failed: detail::is_endian_reversible_inplace<EndianReversibleInplace>::value  
  330 |     BOOST_STATIC_ASSERT( detail::is_endian_reversible_inplace<EndianReversibleInplace>::value );  
      |                                                                                         ^~~~~  
/opt/wandbox/boost-1.71.0/gcc-head/include/boost/static_assert.hpp:70:55: note: in definition of macro 'BOOST_STATIC_ASSERT'  
   70 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                       ^~~~~~~~~~~  
  
```

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-05-02 13:37:06 UTC  
> Updated_at: 2020-05-02 13:37:52 UTC  
> Url: https://github.com/boostorg/endian/pull/44#issuecomment-622954987  

With this fix, the above would compile (on a little-endian platform), because it doesn't actually reverse anything, but `big_to_native_inplace` will still fail, in `endian_reverse`.  
  
A case can be made that both should work. But it's a bit subtle because `endian_reverse` on a normal unscoped enum is often undefined behavior because the reversed value is outside of the valid range of the enum. Interestingly, this is not the case for scoped enums; their valid range is the entire range of the underlying type.  
  
For `endian_reverse_inplace` though, the library can, in principle, support even unscoped enums; as long as nobody actually tries to read the reversed enum, there's no UB.

---

## Comment 2

> Username: pdimov  
> Created_at: 2020-05-03 14:16:59 UTC  
> Url: https://github.com/boostorg/endian/pull/44#issuecomment-623116820  

The given example should now compile with the current develop.

---
