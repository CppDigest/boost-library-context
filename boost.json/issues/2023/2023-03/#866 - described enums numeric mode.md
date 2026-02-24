# #866 - described enums numeric mode [Open]

> Username: KostinPavel  
> Created at: 2023-03-03 07:31:52 UTC  
> Updated at: 2023-03-06 06:06:36 UTC  
> Url: https://github.com/boostorg/json/issues/866  

### Version of Boost   
  
1.81  
  
### Description  
  
Extraction from "underlying" value does not work. Also not covered are "signed underlying" and "unsigned underlying".  
  
### Steps necessary to reproduce the problem  
  
```c++  
#include <boost/describe.hpp>  
#include <boost/json.hpp>  
#include <boost/json/src.hpp>  
  
namespace my_namespace  
{  
enum class enum_class_use_underlying_type : uint8_t  
{  
  var0 = 0,  
  var1 = 1  
};  
BOOST_DESCRIBE_ENUM(enum_class_use_underlying_type)  
}  // namespace my_namespace  
  
#include <iostream>  
  
int main()  
{  
  try {  
    const my_namespace::enum_class_use_underlying_type  
        enum_class_use_underlying_value {  
            my_namespace::enum_class_use_underlying_type::var0};  
  
    const boost::json::value json_value =  
        boost::json::value_from(enum_class_use_underlying_value);  
  
    std::cout << "enum_class_use_underlying_value: " << json_value  
              << std::endl;  // OK  
  
    const auto enum_class_use_underlying_value2 =  
        boost::json::value_to<my_namespace::enum_class_use_underlying_type>(  
            json_value);  // Bug  
  
    return 0;  
  } catch (const std::exception& e) {  
    std::cerr << e.what() << std::endl;  
  }  
  return 1;  
}  
```  
Output:  
```  
enum_class_use_underlying_value: 0  
value is not a string [boost.json:30 at /usr/include/boost/json/detail/value_to.hpp:558:9 in function 'boost::json::result<T> boost::json::detail::value_to_impl(boost::json::try_value_to_tag<T>, const boost::json::value&, described_enum_conversion_tag)']  
```  
  
Because in "boost/json/detail/value_from.hpp" function value_from_helper correct use condition `if( name )`:  
```c++  
// described enums  
template<class T>  
void  
value_from_helper(  
    value& jv,  
    T from,  
    described_enum_conversion_tag)  
{  
    (void)jv;  
    (void)from;  
#ifdef BOOST_DESCRIBE_CXX14  
    char const* const name = describe::enum_to_string(from, nullptr);  
    if( name )  
    {  
        string& str = jv.emplace_string();  
        str.assign(name);  
    }  
    else  
    {  
        using Integer = typename std::underlying_type< remove_cvref<T> >::type;  
        jv = static_cast<Integer>(from);  
    }  
#endif  
}  
```  
but in "boost/json/detail/value_to.hpp" function value_to_impl not using condition `if( name )`:  
```c++  
// described enums  
template<class T>  
result<T>  
value_to_impl(  
    try_value_to_tag<T>,  
    value const& jv,  
    described_enum_conversion_tag)  
{  
    T val = {};  
    (void)jv;  
#ifdef BOOST_DESCRIBE_CXX14  
    error_code ec;  
  
    auto str = jv.if_string();  
    if( !str )  
    {  
        BOOST_JSON_FAIL(ec, error::not_string);  
        return {system::in_place_error, ec};  
    }  
  
    if( !describe::enum_from_string(str->data(), val) )  
    {  
        BOOST_JSON_FAIL(ec, error::unknown_name);  
        return {system::in_place_error, ec};  
    }  
#endif  
  
    return {system::in_place_value, val};  
}  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-03-03 07:41:19 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453106301  

First, what is "'underlying' value"?  
  
Second, at first I thought that I forgot to document that conversion happens from **described** enumerators. But I did document it. So, everything works as documented. Did you forget to describe the enumerators? What are you describing the enum for, if not for enumerators?  
  
What is the behaviour you expected?

---

## Comment 2

> Username: KostinPavel  
> Created at: 2023-03-03 07:48:46 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453113183  

In declare: "enum class enum_class_use_underlying_type : uint8_t"  
uint8_t - is underlying

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-03-03 07:49:47 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453114075  

That's a type, not a value. What's the behaviour you expected?

---

## Comment 4

> Username: KostinPavel  
> Created at: 2023-03-03 07:51:39 UTC  
> Updated at: 2023-03-03 07:54:59 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453115844  

Non-descriptive names should be serialized and deserialized as numbers.  
Serialization works as expected, but deserialization doesn't work as expected.

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-03-03 08:10:29 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453136299  

Ok, so currently the library behaves as documented. And, I guess you ask for a different behaviour?  
  
I'll explain the rationale for our current behaviour. Library-provided conversions are generic and thus try to be the most logical for the types they support. It's logical that for described enums users want to convert to and from strings denoting enumerators.  
  
Converting _from_ a random number to some generic enum is problemtaic, because you cannot programmatically determine the allowed range of values for the enum (it's not always the full domain of the underlying type). Thus we decided to _not allow_ such conversion.  
  
Converting from an enum to its underlying type is never a problem, and we did not want to throw from `value_from`, so we decided to _allow_ such a conversion.  
  
So, now that you know the rationale, what's your rationale for describing an enum, but not its enumerators? If you want  
to just represent enum as a number, you can always provide `value_to/from` overloads  
  
```c++  
namespace my_namespace  
{  
  
void value_from( ::boost::json::value_from_tag, value& jv, enum_class_use_underlying_type e )  
{  
    using UnderlyingType = std::underlying_type< enum_class_use_underlying_type >::type;  
    jv = static_cast< UnderlyingType >(e);  
}  
  
enum_class_use_underlying_type  
value_from( ::boost::json::value_to_tag<enum_class_use_underlying_type>, value const& jv )  
{  
    using UnderlyingType = std::underlying_type< enum_class_use_underlying_type >::type;  
    auto const n = jv.to_number< UnderlyingType >();  
    return static_cast<enum_class_use_underlying_type>(n);  
}  
  
}  
```

---

## Comment 6

> Username: KostinPavel  
> Created at: 2023-03-03 08:16:44 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453142926  

All right. I have to use a solution similar to yours. Yes, I had to enter ranges and/or lists of valid values for "enum". But still there should be a built-in way for the non-string representation of "enum" to ensure compatibility with previous solutions.

---

## Comment 7

> Username: grisumbras  
> Created at: 2023-03-03 08:19:28 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453145967  

What previous solutions? We never provided a generic conversion for described (or any other kind of) enums with a different behaviour.

---

## Comment 8

> Username: KostinPavel  
> Created at: 2023-03-03 08:23:12 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453149893  

Suppose we are expecting a string, but there is a number. It is necessary to at least try to return a value, even without checking the validity of the value. Top-level code can additionally check if the value is correct.  
While in "describe" I did not see the built-in possibility of such a solution.

---

## Comment 9

> Username: KostinPavel  
> Created at: 2023-03-03 08:24:55 UTC  
> Updated at: 2023-03-03 08:27:10 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453151640  

> What previous solutions? We never provided a generic conversion for described (or any other kind of) enums with a different behaviour.  
  
For example, the client and server parts may use different libraries. And for compatibility it is required to be able to work with "enum" as with numbers in JSON.

---

## Comment 10

> Username: grisumbras  
> Created at: 2023-03-03 08:28:23 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453155316  

Ok, so this _does not_ sound like a generic conversion. This is a specific conversion for specific protocol. So, you will have to implement it yourself. Since you already have unearthed implementation for our described enum conversions, you can use it as the basis for your implementation.

---

## Comment 11

> Username: KostinPavel  
> Created at: 2023-03-03 08:33:00 UTC  
> Updated at: 2023-03-03 08:34:00 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453160149  

I have my own solution, and I have been successfully using it for a long time. But then I think that people would not be confused, it is necessary to remove the direct conversion to a number.  
Then they will immediately understand that they need a specific solution for enums as numbers.

---

## Comment 12

> Username: grisumbras  
> Created at: 2023-03-03 08:42:01 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453169985  

I shared your concern when we implemented this. But currently we don't throw from our generic conversions to `value` (unless it's a memory allocation error), so we do not have a way to communicate the error to the user. Maybe I should add a note in the docs to explain the situation with described enums?

---

## Comment 13

> Username: KostinPavel  
> Created at: 2023-03-03 08:54:50 UTC  
> Updated at: 2023-03-03 16:05:16 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453184675  

For example, you can do something like this:  
```c++  
// described enums  
template<class T>  
result<T>  
value_to_impl(  
    try_value_to_tag<T>,  
    value const& jv,  
    described_enum_conversion_tag)  
{  
    T val = {};  
    (void)jv;  
#ifdef BOOST_DESCRIBE_CXX14  
    error_code ec;  
  
    auto str = jv.if_string();  
    auto num = jv.if_int64();  
    auto unum = jv.if_uint64();  
    if(str)  
    {  
      if( !describe::enum_from_string(str->data(), val) )  
      {  
          BOOST_JSON_FAIL(ec, error::unknown_name);  
          return {system::in_place_error, ec};  
      }  
    }  
    else if( num )  
    {  
       char const* const name = describe::enum_to_string(*num, nullptr);  
       if( name )  
       {  
         BOOST_JSON_FAIL(ec, error::not_string);  
         return {system::in_place_error, ec};  
       }  
       val =  static_cast<T>(*num);  
    }  
    else if( unum )  
    {  
       char const* const name = describe::enum_to_string(*unum, nullptr);  
       if( name )  
       {  
         BOOST_JSON_FAIL(ec, error::not_string);  
         return {system::in_place_error, ec};  
       }  
       val = static_cast<T>(*unum);  
    }  
    else  
    {  
        BOOST_JSON_FAIL(ec, error::exhausted_variants);  
        return {system::in_place_error, ec};  
    }  
  
#endif  
  
    return {system::in_place_value, val};  
}  
```

---

## Comment 14

> Username: KostinPavel  
> Created at: 2023-03-03 09:00:26 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453191953  

Worse, I think it's not being done. Those who need to check the values for validity will do this separately.

---

## Comment 15

> Username: KostinPavel  
> Created at: 2023-03-03 09:02:21 UTC  
> Updated at: 2023-03-03 09:06:22 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453194572  

Alternatively, you can add a #define that will serialize and deserialize as a number. In the #define description, you can specify features...  
Or combine these two methods.

---

## Comment 16

> Username: grisumbras  
> Created at: 2023-03-03 09:05:45 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453199613  

This  
```  
val =  static_cast<T>(*num);  
```  
is undefined behaviour for many enums. And we don't have a way to check if it is or isn't.

---

## Comment 17

> Username: KostinPavel  
> Created at: 2023-03-03 09:08:27 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453203195  

Yes, I know. But it comes from compilers and language. You use pointers - although they can also fail. This feature is not easy to take into account - it should not be an obstacle to the operation of the code.

---

## Comment 18

> Username: KostinPavel  
> Created at: 2023-03-03 09:12:47 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453209238  

Someday the check will be built into the language, then everything will work as it should. And now it's stupid to stop the development of the code because of this reason.

---

## Comment 19

> Username: KostinPavel  
> Created at: 2023-03-03 09:14:15 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453211293  

Those who need more guarantees now can take care of it without your checking.

---

## Comment 20

> Username: grisumbras  
> Created at: 2023-03-03 09:14:27 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453211557  

No, it's completely different from pointers. There's no way to defend against UB in that case, thus we _will not_ implement this behaviour.  
  
The point of having generic conversions is that they are uncontroversial. Users can always provide their preferred conversions for their types.

---

## Comment 21

> Username: KostinPavel  
> Created at: 2023-03-03 09:19:11 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453217633  

Checks can and do for pointers as well. A value missing from the enum will not crash the program. This value will be treated as other values. Programmers in code may or may not handle other values. It's not bad - it's just a special case.

---

## Comment 22

> Username: KostinPavel  
> Created at: 2023-03-03 09:22:13 UTC  
> Updated at: 2023-03-03 16:07:06 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453221317  

the only thing to worry about is the size of the type. Might need to add  
`std::numeric_limits<std::underlying_type_t<T>>::min()`  
`std::numeric_limits<std::underlying_type_t<T>>::max()`

---

## Comment 23

> Username: KostinPavel  
> Created at: 2023-03-03 09:26:30 UTC  
> Updated at: 2023-03-03 16:07:27 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453226378  

For example like this:  
```c++  
// described enums  
template<class T>  
result<T>  
value_to_impl(  
    try_value_to_tag<T>,  
    value const& jv,  
    described_enum_conversion_tag)  
{  
    T val = {};  
    (void)jv;  
#ifdef BOOST_DESCRIBE_CXX14  
    error_code ec;  
  
    auto str = jv.if_string();  
    auto num = jv.if_int64();  
    auto unum = jv.if_uint64();  
    if(str)  
    {  
      if( !describe::enum_from_string(str->data(), val) )  
      {  
          BOOST_JSON_FAIL(ec, error::unknown_name);  
          return {system::in_place_error, ec};  
      }  
    }  
    else if( num )  
    {  
       char const* const name = describe::enum_to_string(*num, nullptr);  
       if( name )  
       {  
         BOOST_JSON_FAIL(ec, error::not_string);  
         return {system::in_place_error, ec};  
       }  
       if(*num < std::numeric_limits<std::underlying_type_t<T>>::max() || *num > std::numeric_limits<std::underlying_type_t<T>>::max())  
       {  
         BOOST_JSON_FAIL(ec, error::size_mismatch);  
         return {system::in_place_error, ec};  
       }         
       val =  static_cast<T>(*num);  
    }  
    else if( unum )  
    {  
       char const* const name = describe::enum_to_string(*num, nullptr);  
       if( name )  
       {  
         BOOST_JSON_FAIL(ec, error::not_string);  
         return {system::in_place_error, ec};  
       }  
       if(*unum < std::numeric_limits<std::underlying_type_t<T>>::max() || *unum > std::numeric_limits<std::underlying_type_t<T>>::max())  
       {  
         BOOST_JSON_FAIL(ec, error::size_mismatch);  
         return {system::in_place_error, ec};  
       }  
       val = static_cast<T>(*unum);  
    }  
  
    BOOST_JSON_FAIL(ec, error::exhausted_variants);  
    return {system::in_place_error, ec};  
  
#endif  
  
    return {system::in_place_value, val};  
}  
```

---

## Comment 24

> Username: grisumbras  
> Created at: 2023-03-03 09:26:43 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453226657  

There are actual compiler optimizations that take advantage of the allowed enum range. Your program _may_ crash. It _may_ behave erratically. That's what "undefined behaviour" means.

---

## Comment 25

> Username: KostinPavel  
> Created at: 2023-03-03 09:28:17 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453228564  

The above example addresses this issue.

---

## Comment 26

> Username: grisumbras  
> Created at: 2023-03-03 09:28:18 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453228589  

There's no point in explaining to me how to amend the current implementation to get this or that behaviour. I've wrote those functions, I know how to change them. But I'm telling you, we're not adding behaviour that silently enables UB.

---

## Comment 27

> Username: grisumbras  
> Created at: 2023-03-03 09:28:32 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453228855  

No, it does not

---

## Comment 28

> Username: grisumbras  
> Created at: 2023-03-03 09:29:23 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453229908  

```  
enum E {a=1, b=2};  
int n = 4;  
E e = static_cast<E>(n); // UB  
```

---

## Comment 29

> Username: KostinPavel  
> Created at: 2023-03-03 09:30:14 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453230885  

Explain to me, please, why?  
What am I not considering?

---

## Comment 30

> Username: KostinPavel  
> Created at: 2023-03-03 09:31:17 UTC  
> Updated at: 2023-03-03 16:08:34 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453232173  

> ```c++  
> enum E {a=1, b=2};  
> int n = 4;  
> E e = static_cast<E>(n); // UB  
> ```  
  
Please add `std::numeric_limits<std::underlying_type_t>::min()` `std::numeric_limits<std::underlying_type_t>::max()` test to this code.

---

## Comment 31

> Username: KostinPavel  
> Created at: 2023-03-03 09:37:30 UTC  
> Updated at: 2023-03-03 16:08:58 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453239731  

```c++  
if(n <std::numeric_limit<std::underlying_type_t<E>::min() || n >std::numeric_limit<std::underlying_type_t<E>::max())  
{  
throw ....  
}  
E e = static_cast<E>(n); // now correct  
  switch (e)  
  {  
  case E::a:  
    /* code */  
    break;  
  case E::b:  
    /* code */  
    break;  
    
  default:  
    /* over values */  
    break;  
  }  
```

---

## Comment 32

> Username: grisumbras  
> Created at: 2023-03-03 10:08:49 UTC  
> Updated at: 2023-03-03 10:10:14 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453281985  

For an enum without a fixed unerlying type the range of its values is the range of values that is possible to represent with the smallest bitfiled that can represent every enum's enumerator. See here: https://eel.is/c++draft/enum#dcl.enum-8  
  
The allowed range for `E`, in this case is [1; 3]. 4 is outside the allowed range. But it is not outside the allowed range of the underlying type, which is not specified, but on most implementations will be at least [0; 256] or [-128; 127].  
  
Look at this example: https://godbolt.org/z/TTWYbKTjx  
Notice how the function is compiled to never return "unnkown", even though the underlying type clearly supprorts values larger than 3.

---

## Comment 33

> Username: KostinPavel  
> Created at: 2023-03-03 10:22:54 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453299807  

All right. This only confirms my decision.  
The base type will still be assigned.  
And the code will work correctly.

---

## Comment 34

> Username: KostinPavel  
> Created at: 2023-03-03 10:28:08 UTC  
> Updated at: 2023-03-03 16:09:21 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453306526  

try it in [online](https://godbolt.org/)  
```c++  
#include <limits>  
#include <type_traits>  
#include <stdexcept>  
#include <iostream>  
  
  
enum E {a=1, b};  
  
static_assert( std::numeric_limits< std::underlying_type_t<E> >::max() > 4 );  
  
char const* message(E e) {  
    switch( e ) {  
    case a: return "a";  
    case b: return "b";  
    case E(3): return "3";  
    default: return "unknown";  
    }  
}  
  
int main()  
{  
    try  
    {  
        std::cout << std::numeric_limits<std::underlying_type_t<E> >::min() << std::endl;  
        std::cout << std::numeric_limits<std::underlying_type_t<E> >::max() << std::endl;  
        int n = 4;  
        if(n < std::numeric_limits<std::underlying_type_t<E> >::min() || n > std::numeric_limits<std::underlying_type_t<E> >::max())  
        {  
            throw std::runtime_error("overflow");  
        }  
        E e = static_cast<E>(n);      
    } catch (const std::exception& e) {  
    std::cerr << e.what() << std::endl;  
    }  
  return 0;  
}  
```

---

## Comment 35

> Username: grisumbras  
> Created at: 2023-03-03 10:33:35 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453313498  

What does that snippet examplify? If your check would have made a difference an exception would have been thrown. But it doesn't happen. https://godbolt.org/z/Pa7989Y5P

---

## Comment 36

> Username: KostinPavel  
> Created at: 2023-03-03 10:40:04 UTC  
> Updated at: 2023-03-03 16:09:39 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453321571  

Compare to this:  
```c++  
#include <limits>  
#include <type_traits>  
#include <stdexcept>  
#include <iostream>  
  
  
enum E {a=1, b};  
  
static_assert( std::numeric_limits< std::underlying_type_t<E> >::max() > 4 );  
  
int main()  
{  
    E e = a;  
    try  
    {  
        std::cout << std::numeric_limits<std::underlying_type_t<E> >::min() << std::endl;  
        std::cout << std::numeric_limits<std::underlying_type_t<E> >::max() << std::endl;  
        uint64_t n = 4294967295 + 10;  
        if(n < std::numeric_limits<std::underlying_type_t<E> >::min() || n > std::numeric_limits<std::underlying_type_t<E> >::max())  
        {  
            throw std::runtime_error("overflow");  
        }  
        e = static_cast<E>(n);      
    } catch (const std::exception& e) {  
        std::cerr << e.what() << std::endl;  
    }  
    return 0;  
}  
```

---

## Comment 37

> Username: KostinPavel  
> Created at: 2023-03-03 10:42:54 UTC  
> Updated at: 2023-03-03 10:44:20 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453325198  

This demonstrates that UB is being processed.  
And there will be no unforeseen situations.

---

## Comment 38

> Username: KostinPavel  
> Created at: 2023-03-03 10:47:37 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453333765  

Any value outside the definition will either default case or rise exception an overflow. Both situations are expected.

---

## Comment 39

> Username: grisumbras  
> Created at: 2023-03-03 10:49:06 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453335661  

No, the value 4 does not result in a thrown exception. You seem to ignore what I'm saying you. Are you trying to say that the value 4 is allowed for enum E?

---

## Comment 40

> Username: KostinPavel  
> Created at: 2023-03-03 10:51:18 UTC  
> Updated at: 2023-03-03 16:10:09 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453340849  

Why should?  
```c++  
enum {  
a=1,  
b=5  
};  
```

---

## Comment 41

> Username: KostinPavel  
> Created at: 2023-03-03 10:52:34 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453343273  

"4" - it's just another value.

---

## Comment 42

> Username: grisumbras  
> Created at: 2023-03-03 10:53:18 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453344610  

No, 4 is outside of the range of [0;3] which is the range of enum E {a=1, b};

---

## Comment 43

> Username: KostinPavel  
> Created at: 2023-03-03 10:54:06 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453345701  

Imagine that it is simply not described. Perhaps up to a certain point in time.

---

## Comment 44

> Username: KostinPavel  
> Created at: 2023-03-03 10:55:34 UTC  
> Updated at: 2023-03-03 16:10:50 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453347615  

You have a slightly distorted idea of enumerations. How then do you explain the missing definitions to yourself?  
```c++  
enum {  
a=1,  
b=5  
};  
```

---

## Comment 45

> Username: KostinPavel  
> Created at: 2023-03-03 10:58:00 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453350935  

Treat them as numbers, some of which have a name.

---

## Comment 46

> Username: grisumbras  
> Created at: 2023-03-03 10:58:50 UTC  
> Updated at: 2023-03-03 11:03:55 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453352104  

I don't have a distorted idea of enumerations. I've linked to a clause in the C++ standard where it is explained what a valid range of allowed values for an enumeration is. For enum {a=1, b = 5} the valid range is [0; 7] (in other words, everything that can be represented by 3 bits, which is how many bits required for the number 5).

---

## Comment 47

> Username: grisumbras  
> Created at: 2023-03-03 11:02:02 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453356434  

Here, a crash. https://godbolt.org/z/s8sjqxqa6

---

## Comment 48

> Username: KostinPavel  
> Created at: 2023-03-03 11:05:05 UTC  
> Updated at: 2023-03-03 16:11:29 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453360341  

But the standard does not say anything about what to do with other values. Including missed ones.  
And if the compiler allocated 3 bits for enumeration, then `std::numeric_limits<std::underlying_type_t<E> >::min() `and `std::numeric_limits<std::underlying_type_t<E> >::max() `would beat the same by others.

---

## Comment 49

> Username: grisumbras  
> Created at: 2023-03-03 11:06:44 UTC  
> Updated at: 2023-03-03 11:07:05 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453362364  

What are you talking about? It compiles and crashes at runtime.

---

## Comment 50

> Username: KostinPavel  
> Created at: 2023-03-03 11:09:36 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453365887  

> What are you talking about? It compiles and crashes at runtime.  
This is a programmer's mistake. He didn't check the range.

---

## Comment 51

> Username: KostinPavel  
> Created at: 2023-03-03 11:10:32 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453366966  

The proposed solution has a check.

---

## Comment 52

> Username: KostinPavel  
> Created at: 2023-03-03 11:12:35 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453369300  

> Here, a crash. https://godbolt.org/z/s8sjqxqa6  
  
The library will not protect against such an error.

---

## Comment 53

> Username: KostinPavel  
> Created at: 2023-03-03 11:15:06 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453372174  

Fine. I tried to justify all my thoughts. You decide. The developer is you.  
My solution uses the described approach.  
Whether or not this approach is available to others is up to you.

---

## Comment 54

> Username: KostinPavel  
> Created at: 2023-03-03 11:16:42 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453373958  

The only other way I can help is to make a branch with a pull request.

---

## Comment 55

> Username: grisumbras  
> Created at: 2023-03-03 11:22:52 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453381174  

There's no way to check if that conversion will be UB or not. If you know how to do it, please change the example I provided (the one with a crashing program) so that it does not crash. Then we can discuss whether your change is possible to integrate into the library. Things you've suggested so far _would not_ have solved the problem, because they do not address the problem in any way.

---

## Comment 56

> Username: KostinPavel  
> Created at: 2023-03-03 11:28:09 UTC  
> Updated at: 2023-03-03 16:11:53 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453387207  

`static_assert` is almost the same as "throw" but the handler is not defined by the programmer. All you have to do is check the range when the values come in at runtime.  
It is important that the proposal does not make it worse!

---

## Comment 57

> Username: grisumbras  
> Created at: 2023-03-03 11:33:20 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453392966  

I repeat, I can't check the range. Unless I'm missing something. Please, show me in CE how to do that, by making the program to not crash.

---

## Comment 58

> Username: KostinPavel  
> Created at: 2023-03-03 11:38:39 UTC  
> Updated at: 2023-03-03 16:12:09 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453399239  

```c++  
#include <limits>  
#include <cstdio>  
#include <stdexcept>  
  
enum E {a=1, b};  
  
char const* message(E e) {  
    switch( e ) {  
    case a: return "a";  
    case b: return "b";  
    //case E(0): return "0"; // this programmer error use sanitazer to detect this  
    //case E(3): return "3"; // this programmer error use sanitazer to detect this  
    default: return "something else";  
    }  
}  
  
int main(int argc, char**)  
{      
    try  
    {  
    argc += 3;       
    if(argc < std::numeric_limits<std::underlying_type_t<E> >::min() || argc > std::numeric_limits<std::underlying_type_t<E> >::max())  
    {  
        throw std::runtime_error("overflow");  
    }  
    E e = static_cast<E>(argc);  
    std::printf(message(e));  
    } catch (const std::exception& e) {  
        std::printf(e.what());          
    }  
    return 0;  
}  
```

---

## Comment 59

> Username: KostinPavel  
> Created at: 2023-03-03 11:45:21 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453410495  

In this library, there is no need to iterate over the values in switch!

---

## Comment 60

> Username: grisumbras  
> Created at: 2023-03-03 11:47:03 UTC  
> Updated at: 2023-03-03 11:47:38 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453412387  

You did not catch the the value being outside of enum's range. You just changed the code so that UB would not manifest. So, you don't know how to check the range either. Of course, it's because there's no way to do that.  
  
Just to reiterate, I _will not_ add silent undefined behaviour to the library. Users are free to write their own conversions, which can have as much UB as they want. They also may solve the issue by only using enums with fixed underlying types. But as generic conversions have to be generic, there's no acceptable solution on our end.

---

## Comment 61

> Username: KostinPavel  
> Created at: 2023-03-03 11:53:12 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453419066  

I think you are wrong.  
In the proposed solution, UB also remain on the side of the developer. But they do not need to finish your decision with their crutches.

---

## Comment 62

> Username: KostinPavel  
> Created at: 2023-03-03 11:54:26 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453420427  

The code remains transparent within the capabilities of the language and the compiler.

---

## Comment 63

> Username: KostinPavel  
> Created at: 2023-03-03 11:55:43 UTC  
> Updated at: 2023-03-03 11:56:38 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453421839  

Do you think someone expects that it will be converted to a number, but not back?  
This is truly hidden behavior!

---

## Comment 64

> Username: grisumbras  
> Created at: 2023-03-03 11:59:17 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453426157  

It's defined by definition. Because it is documented. I will consider adding a note to make this more explicit.

---

## Comment 65

> Username: KostinPavel  
> Created at: 2023-03-03 13:28:16 UTC  
> Updated at: 2023-03-03 16:12:46 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453534107  

This is code from the library's user space!  
```c++  
char const* message(E e) {  
    switch( e ) {  
    case a: return "a";  
    case b: return "b";  
    //case E(0): return "0"; // this programmer error use sanitazer to detect this  
    //case E(3): return "3"; // this programmer error use sanitazer to detect this  
    default: return "something else";  
    }  
}  
```

---

## Comment 66

> Username: KostinPavel  
> Created at: 2023-03-03 13:47:04 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453556744  

"-fstrict-enums" also set by the library user

---

## Comment 67

> Username: KostinPavel  
> Created at: 2023-03-03 13:51:52 UTC  
> Updated at: 2023-03-03 16:13:39 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453562415  

example of a warning to a library user:  
```  
C:\msys64\mingw64\bin\g++.exe main.cpp -o d:\VSCode\CPP\enum_test\output\main.exe -Wall -Wextra -g3   
  
main.cpp: In function 'const char* message(E)':  
main.cpp:10:5: warning: case value '0' not in enumerated type 'E' [-Wswitch]  
   10 |     case E(0): return "0";  
      |     ^~~~  
main.cpp:11:5: warning: case value '3' not in enumerated type 'E' [-Wswitch]  
   11 |     case E(3): return "3";  
      |     ^~~~  
```

---

## Comment 68

> Username: KostinPavel  
> Created at: 2023-03-03 14:01:20 UTC  
> Updated at: 2023-03-03 16:14:10 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453575139  

> > Here, a crash. https://godbolt.org/z/s8sjqxqa6  
>   
> The library will not protect against such an error.  
  
  
Maybe a bug in https://godbolt.org/  
Because it worked for me as expected.  
  
```  
D:\VSCode\CPP\enum_test\output> gcc --version  
gcc.exe (Rev10, Built by MSYS2 project) 12.2.0  
C:\msys64\mingw64\bin\g++.exe main.cpp -o d:\VSCode\CPP\enum_test\output\main.exe -std=c++20 -O3 -fstrict-enums   
PS D:\VSCode\CPP\enum_test> cd 'd:\VSCode\CPP\enum_test\output'  
PS D:\VSCode\CPP\enum_test\output> & .\'main.exe'  
3  
PS D:\VSCode\CPP\enum_test\output>  
  
```

---

## Comment 69

> Username: KostinPavel  
> Created at: 2023-03-03 14:10:28 UTC  
> Updated at: 2023-03-03 16:18:13 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453588976  

Crashes because argc is not set or optimized in emulator in [site](https://godbolt.org/). Try this.  
  
```c++  
int main(int argc, char**)  
{  
    argc = 0;  
    E e = static_cast<E>(argc + 3);  
    std::printf(message(e));  
    return 0;  
}  
```  
or this  
```c++  
int main(int argc, char**)  
{  
    argc = 10;  
    E e = static_cast<E>(argc + 3);  
    std::printf(message(e));  
    return 0;  
}  
```  
it Works.

---

## Comment 70

> Username: KostinPavel  
> Created at: 2023-03-03 14:26:28 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453610682  

Please let us know if it worked for you.

---

## Comment 71

> Username: KostinPavel  
> Created at: 2023-03-03 15:23:43 UTC  
> Updated at: 2023-03-03 16:15:15 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453694070  

I also tried it:  
```c++  
#include <limits>  
#include <cstdio>  
  
  
enum E {a=1, b};  
  
char const* message(E e) {  
    switch( e ) {  
    case a: return "a";  
    case b: return "b";  
    case E(0): return "0";  
    case E(3): return "3";  
    default: return "something else";  
    }  
}  
  
int main(int argc, char**)  
{  
    argc = std::numeric_limits<int>::max();  
    std::printf("%i\n",argc);  
    std::printf("%i\n",argc+3);  
    E e = static_cast<E>(argc + 3);  
    std::printf(message(e));  
    return 0;  
}  
```  
It works too:  
```  
ASM generation compiler returned: 0  
Execution build compiler returned: 0  
Program returned: 0  
2147483647  
-2147483646  
something else  
```

---

## Comment 72

> Username: KostinPavel  
> Created at: 2023-03-03 15:27:02 UTC  
> Updated at: 2023-03-03 15:48:49 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453698515  

I don't think you'll find a correct case that doesn't work.

---

## Comment 73

> Username: pdimov  
> Created at: 2023-03-03 17:39:45 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453875045  

> This  
>   
> ```  
> val =  static_cast<T>(*num);  
> ```  
>   
> is undefined behaviour for many enums.  
  
In this specific case not, because the value has been checked to have a corresponding enumerator (via enum_to_string).

---

## Comment 74

> Username: pdimov  
> Created at: 2023-03-03 17:41:32 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453876943  

> ```c++  
> enum E {a=1, b};  
>   
> char const* message(E e) {  
>     switch( e ) {  
>     case a: return "a";  
>     case b: return "b";  
>     case E(0): return "0";  
>     case E(3): return "3";  
>     default: return "something else";  
>     }  
> }  
> ```  
  
This is OK because 3 is in the range of the enum (which is 0..3 in this case because it has to contain the values 1 and 2). But E(4) would be UB.

---

## Comment 75

> Username: pdimov  
> Created at: 2023-03-03 18:07:56 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1453906402  

The rule is, if the value can't fit in the allowed range of the enum, behavior is undefined. The allowed range is: if the enum has a fixed underlying type, the range is that of the type; otherwise, the range is the range of the hypothetical integral type with the smallest number of bits that can fit all the enumerators. So  
  
```  
enum E1 { a = 1, b = 5 }; // range is 0..7  
enum E2: int {}; // range is that of int  
enum class E3: int {}; // range is that of int  
enum class E4 {}; // range is that of int, because enum class always has a fixed underlying type  
```  
  
So in principle, we can check is_scoped_enum (which is implementable as in https://github.com/boostorg/endian/blob/develop/include/boost/endian/detail/is_scoped_enum.hpp), and there's no UB there for any `underlying_type` value.  
  
But there's no reliable way to detect is_fixed_enum (E2 above.) I have a trait that kind of does (https://github.com/boostorg/endian/blob/feature/is-fixed-enum/include/boost/endian/detail/is_fixed_enum.hpp) but it requires C++17 and doesn't quite work correctly on all compilers.  
  
And there's absolutely no way to obtain the range of E1 (except if it's correctly described, in which case we can compute it from the enumerator descriptors).

---

## Comment 76

> Username: KostinPavel  
> Created at: 2023-03-03 19:32:43 UTC  
> Updated at: 2023-03-03 19:42:57 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1454030284  

Perhaps you need to make implementations for each of the four options.  
```c++  
enum E1 { a = 1, b = 5 }; // range is 0..7 // ??? most compilers will choose a chunk of memory that is a multiple of a byte for this type  
enum E2: int {}; // range is that of int //(std::is_scoped_enum) does not require discussion, since the solution is proposed   
enum class E3: int {}; // range is that of int // does not require discussion, since the solution is proposed  
enum class E4 {}; // range is that of int, because enum class always has a fixed underlying type //does not require discussion, since the solution is proposed  
```

---

## Comment 77

> Username: KostinPavel  
> Created at: 2023-03-03 19:45:39 UTC  
> Updated at: 2023-03-03 19:51:18 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1454047769  

It's a good idea to compare values from string descriptions, but it's not clear how to convert to a number then?  
Maybe some kind of template specifier or define is required?  
```c++  
if( num )  
{  
       char const* const name = describe::enum_to_string(*num, nullptr);  
       if( name )  
       {  
         ...  
```

---

## Comment 78

> Username: pdimov  
> Created at: 2023-03-03 19:51:15 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1454052720  

> ```c++  
> // ??? most compilers will choose a chunk of memory that is a multiple of a byte for this type  
> ```  
  
It's still undefined behavior. Bad things will happen if not today, tomorrow.  
  
In fairness, -fsanitize=undefined doesn't diagnose this for both GCC and Clang, and it seems to be unspecified before C++17, not undefined.

---

## Comment 79

> Username: KostinPavel  
> Created at: 2023-03-03 19:58:56 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1454060108  

to detect the numeric mode, you can use:  
```c++  
//default  
template<typename T>  
constexpr bool need_numeric_mode = false;  
  
//specialization:  
template<my_enum_type>  
constexpr bool need_numeric_mode = true;  
```

---

## Comment 80

> Username: KostinPavel  
> Created at: 2023-03-03 20:11:31 UTC  
> Updated at: 2023-03-06 06:06:36 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1454073165  

The structure of the code is something like this:  
```c++  
// described enums  
template<class T>  
result<T>  
value_to_impl(  
    try_value_to_tag<T>,  
    value const& jv,  
    described_enum_conversion_tag)  
{  
    T val = {};  
    (void)jv;  
#ifdef BOOST_DESCRIBE_CXX14  
    error_code ec;  
  
    if constexpr (need_numeric_mode<T>)  
    {  
        auto num = jv.if_int64();  
        auto unum = jv.if_uint64();  
        if( num )  
        {  
            char const* const name = describe::enum_to_string(*num, nullptr);  
            if( !name ) // detection unnamed variants!!!  
            {  
                BOOST_JSON_FAIL(ec, error::value);  
                return {system::in_place_error, ec};  
            }  
            val =  static_cast<T>(*num);  
        }  
        else if( unum )  
        {  
            char const* const name = describe::enum_to_string(*unum, nullptr);  
            if( !name )  
            {  
                BOOST_JSON_FAIL(ec, error::value);  
                return {system::in_place_error, ec};  
            }  
            val =  static_cast<T>(*unum);  
        }  
        else  
        {  
            BOOST_JSON_FAIL(ec, error::not_number);  
            return {system::in_place_error, ec};  
        }  
    }  
    else  
    {  
        auto str = jv.if_string();  
        if(!str)  
        {  
            BOOST_JSON_FAIL(ec, error::not_string);  
            return {system::in_place_error, ec};  
        }  
        if( !describe::enum_from_string(str->data(), val) )  
        {  
            BOOST_JSON_FAIL(ec, error::unknown_name);  
            return {system::in_place_error, ec};  
        }  
    }  
  
#endif  
  
    return {system::in_place_value, val};  
}  
```

---

## Comment 81

> Username: KostinPavel  
> Created at: 2023-03-03 20:13:30 UTC  
> Url: https://github.com/boostorg/json/issues/866#issuecomment-1454075500  

Something similar is needed when converting to a number.
