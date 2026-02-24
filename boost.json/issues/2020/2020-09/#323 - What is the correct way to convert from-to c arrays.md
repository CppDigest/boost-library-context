# #323 - What is the correct way to convert from/to c arrays? [Closed]

> Username: metab0t  
> Created at: 2020-09-12 14:07:28 UTC  
> Updated at: 2020-09-13 07:40:23 UTC  
> Closed at: 2020-09-13 07:40:22 UTC  
> Url: https://github.com/boostorg/json/issues/323  

I wonder what is the correct way to use ser/der of custom struct containing c arrays.  
  
For example, we have following struct:  
  
```cpp  
constexpr int length = 100;  
  
struct S  
{  
    char name[64];  
    double prices[length];  
};  
```  
  
In `nlohmann::json`, I can write follwing code:  
  
```cpp  
#include "json.hpp"  
  
using njson = nlohmann::json;  
  
inline void from_json(const njson &j, S &s)  
{  
    strcpy(s.name, j.at("name").get<std::string>().c_str());  
    j.at("prices").get_to(s.prices);  
}  
  
inline void to_json(njson &j, const S &s)  
{  
    j["name"] = s.name;  
    j["prices"] = s.prices;  
}  
```  
  
I attempt to write equivalent ser/der code in boost.json:  
  
```cpp  
#define BOOST_JSON_STANDALONE  
#include <boost/json.hpp>  
#include <boost/json/src.hpp>  
  
namespace bj = boost::json;  
  
void tag_invoke(bj::value_from_tag, bj::value &jv, S const &s)  
{  
    bj::object obj;  
    obj["name"] = bj::value_from(s.name);  
    obj["prices"] = bj::value_from(s.prices);  
    jv = obj;  
}  
  
S tag_invoke(bj::value_to_tag<S>, bj::value const &jv)  
{  
    S s;  
    bj::object const &obj = jv.as_object();  
    strcpy(s.name, obj.at("name").as_string().c_str());  
    memcpy(s.prices, obj.at("prices").as_array().data(), sizeof(s.prices));  
    return s;  
}  
```  
  
However, the `memcpy` seems ugly and gets wrong result.  
  
Would you love to provide some instructions on idiomatic way to convert from/to c arrays?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-12 14:31:50 UTC  
> Url: https://github.com/boostorg/json/issues/323#issuecomment-691498406  

`value_from` on C arrays works because the array meets the syntactic requirements for a generic container that the library knows what to do with. In particular that `std::begin` and `std::end` are well-formed and dereference to something the library can convert:  
https://github.com/CPPAlliance/json/blob/8bf9ebfd1bcea6b67de39cfd858064d727a03c7c/include/boost/json/detail/value_traits.hpp#L42  
  
C language arrays meet this requirement.  
  
`value_to` is a different story. The generic algorithms expect a container that supports a dynamic size. That is, a container which can be resized to fit the necessary number of elements. C language arrays do not meet this requirement. And implementing support for your use-case is problematic, because C language arrays don't neatly fit into the _Container_ requirements.  
  
In fact your code is dangerous. This statement can cause a buffer overflow if the string whose key matches "name" is larger than the size of s.name:  
```  
strcpy(s.name, obj.at("name").as_string().c_str());  
```  
  
This code is also dangerous as it will either access memory beyond the source buffer or it will leave some elements in the destination array uninitialized:  
```  
memcpy(s.prices, obj.at("prices").as_array().data(), sizeof(s.prices));  
```  
Of course, the larger problem with the statement above, is that the `value_type` of `json::array` is `json::value` and not `double` which the code above assumes.  
  
What I would do is make my own simple wrapper around a C style array, and provide an overload of `tag_invoke` for it. Something like this:  
```  
    template<class T, std::size_t N>  
    class array_ref  
    {  
        T* data_;  
  
    public:  
        explicit array_ref(T* data) noexcept  
            : data_(data)  
        {  
        }  
  
        static constexpr std::size_t size()  
        {  
            return N;  
        }  
  
        T* data()  
        {  
            return data_;  
        }  
    };  
  
    template<class T, std::size_t N>  
    array_ref<T, N>  
    make_array_ref( T(&arr)[N] )  
    {  
        return array_ref<T, N>(&arr[0]);  
    }  
```  
  
Then you might write:  
```  
obj[ "prices" ] = bj::value_from( make_array_ref(s.prices) );  
```  
  
For converting a `json::value` to a C style array, the `value_to` system cannot be used, as C style arrays do not have constructors. You would need to write out a loop for that.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-12 14:38:06 UTC  
> Updated at: 2020-09-12 14:40:54 UTC  
> Url: https://github.com/boostorg/json/issues/323#issuecomment-691499492  

Just a minor point in your original code there are a few problems  
```  
    bj::object obj;  
    obj["name"] = bj::value_from(s.name);  
    obj["prices"] = bj::value_from(s.prices);  
    jv = obj;  
```  
  
* The first two assignments cause unnecessary construction of null values  
* The last assignment performs an unnecessary copy  
* The storage is not propagated from the value argument  
  
This is idiomatic:  
```  
    object& obj = jv.emplace_object();  
    obj.emplace( "name", bj::value_from( s.name, jv.storage() ) );  
    obj.emplace( "prices", bj::value_from( s.prices, jv.storage() ) );  
```  
  
Note that the inefficiencies in your original code are also present in nlohmann JSON, except that they cannot be mitigated.

---

## Comment 3

> Username: metab0t  
> Created at: 2020-09-12 14:53:55 UTC  
> Url: https://github.com/boostorg/json/issues/323#issuecomment-691502064  

Thanks a lot! @vinniefalco   
  
I will try your `array_ref` wrapper code to have a test.  
  
Your second comment also enlightens me a lot. I tried hard to avoid the copy but failed today.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-12 14:58:28 UTC  
> Url: https://github.com/boostorg/json/issues/323#issuecomment-691502676  

Something like this might work for converting a JSON array into a C style array:  
```  
template<class T, std::size_t N>  
void  
fill_array( T(&arr)[N], value const& jv )  
{  
    array const* p = jv.is_array();  
    if(! p)  
        throw std::invalid_argument( "not array" );  
    if(p->size() != N)  
        throw std::length_error( "wrong size" );  
    for( std::size_t i = 0; i < N; ++i )  
        arr[i] = value_to< T >( (*p)[i] );  
}  
```  
  
Note however that it does not compose, so it isn't quite as powerful as overloading `tag_invoke` (which is not possible, since C style arrays do not have constructors)

---

## Comment 5

> Username: metab0t  
> Created at: 2020-09-12 15:14:15 UTC  
> Url: https://github.com/boostorg/json/issues/323#issuecomment-691504515  

>   
>   
> Something like this might work for converting a JSON array into a C style array:  
>   
> ```  
> template<class T, std::size_t N>  
> void  
> fill_array( T(&arr)[N], value const& jv )  
> {  
>     array const* p = jv.is_array();  
>     if(! p)  
>         throw std::invalid_argument( "not array" );  
>     if(p->size() != N)  
>         throw std::length_error( "wrong size" );  
>     for( std::size_t i = 0; i < N; ++i )  
>         arr[i] = value_to< T >( (*p)[i] );  
> }  
> ```  
>   
> Note however that it does not compose, so it isn't quite as powerful as overloading `tag_invoke` (which is not possible, since C style arrays do not have constructors)  
  
Thanks! I will try this helper function.

---

## Comment 6

> Username: metab0t  
> Created at: 2020-09-13 07:40:22 UTC  
> Url: https://github.com/boostorg/json/issues/323#issuecomment-691628461  

I will close this issue and decide to use for-loop to read json value into c arrays.
