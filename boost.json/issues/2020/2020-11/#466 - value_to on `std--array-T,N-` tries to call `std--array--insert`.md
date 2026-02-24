# #466 - value_to on `std::array<T,N>` tries to call `std::array::insert` [Closed]

> Username: ennis  
> Created at: 2020-11-17 10:30:17 UTC  
> Updated at: 2021-06-26 18:18:53 UTC  
> Closed at: 2021-06-26 18:18:53 UTC  
> Url: https://github.com/boostorg/json/issues/466  

### Version of Boost  
  
Boost.JSON in standalone mode, commit 099a82957a75bf088a8bc45a379f81b3c6661d18  
  
### Steps necessary to reproduce the problem  
  
This snippet fails to compile:   
```cpp  
#include <boost/json.hpp>  
#include <array>  
  
/*// Specialization of JSON conversions for std::array<T,N>  
template <typename T, size_t N>  
void tag_invoke(const boost::json::value_from_tag &, boost::json::value &jv,  
                std::array<T, N> const &t) {  
  jv = boost::json::array{t.begin(), t.end()};  
}  
  
template <typename T, size_t N>  
std::array<T, N> tag_invoke(const boost::json::value_to_tag<std::array<T, N>> &,  
                            boost::json::value const &jv) {  
  std::array<T, N> outArray;  
  const auto       arrayJson = jv.as_array();  
  for (size_t i = 0; i < N; ++i) {  
    outArray[i] = arrayJson.at(i);  
  }  
  return outArray;  
}*/  
  
int main()  
{  
    namespace json = boost::json;  
    json::array arrayJson{ {1,2,3,4} };  
    static_assert(json::has_value_to<std::array<float,4>>::value);  
    auto array = json::value_to<std::array<float, 4>>(json::value{ arrayJson });  
}  
```  
with the following error (MSVC 16 (19.27.29111) ):  
```  
_deps\boost_json-src\include\boost/json/detail/value_to.hpp(154,16): error C2039: 'insert': is not a member of 'std::array<float,4>'  
json-test.cpp(26): message : see declaration of 'std::array<float,4>'  
_deps\boost_json-src\include\boost/json/detail/value_to.hpp(171): message : see reference to function template instantiation 'T boost::json::standalone::detail::value_to_generic<T,0x0>(const boost::json::standalone::value &,boost::json::standalone::detail::priority_tag<0>)' being compiled  
        with  
        [  
            T=std::array<float,4>  
        ]  
_deps\boost_json-src\include\boost/json/detail/value_to.hpp(187): message : see reference to function template instantiation 'T boost::json::standalone::detail::tag_invoke<std::array<float,4>,0x0>(boost::json::standalone::value_to_tag<std::array<float,4>>,const boost::json::standalone::value &)' being compiled  
        with  
        [  
            T=std::array<float,4>  
        ]  
_deps\boost_json-src\include\boost/json/value_to.hpp(90): message : see reference to function template instantiation 'T boost::json::standalone::detail::value_to_impl<std::array<float,4>,0x0>(boost::json::standalone::value_to_tag<std::array<float,4>>,const boost::json::standalone::value &)' being compiled  
        with  
        [  
            T=std::array<float,4>  
        ]  
json-test.cpp(27): message : see reference to function template instantiation 'T boost::json::standalone::value_to<std::array<float,4>,boost::json::standalone::value,0x0>(const U &)' being compiled  
        with  
        [  
            T=std::array<float,4>,  
            U=boost::json::standalone::value  
        ]  
```  
  
I'm not sure whether the snippet should compile or not (i.e. whether value_to should be supported with fixed-size containers: see also #323). However it seems that, for `std::array`, the internal overloads of tag_invoke for container-like types are nonetheless selected, and they require the `insert` method which `std::array` doesn't provide.  
Maybe the ToContainerLike named requirements should also include the presence of the "insert" method on the type, if it's possible to detect it?  
  
A consequence of that is that I am unable to provide my own overloads of `tag_invoke(value_to_tag)` for `std::array<T,N>` as they conflict with the generic overload provided by the library.   
  
In addition, `value_to` doesn't seem to support passing it anything other than `json::value`s, even though #304 seems to suggests that there should be overloads for arrays,objects and strings.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-11-17 15:00:04 UTC  
> Url: https://github.com/boostorg/json/issues/466#issuecomment-728985006  

To work, your tag_invoke overloads need to be in an associated namespace for one of the arguments. This means that they need to be in one of the namespaces `boost`, `boost::json`, or `std`. The right (or rather, least wrong) choice out of these three is `boost::json` because putting things into `std` is forbidden.  
  
Although `std::array` should be handled natively (that is, it's a legitimate issue that it doesn't work out of the box today.)

---

## Comment 2

> Username: ennis  
> Created at: 2020-11-17 15:26:56 UTC  
> Url: https://github.com/boostorg/json/issues/466#issuecomment-729002752  

> To work, your tag_invoke overloads need to be in an associated namespace for one of the arguments. This means that they need to be in one of the namespaces `boost`, `boost::json`, or `std`. The right (or rather, least wrong) choice out of these three is `boost::json` because putting things into `std` is forbidden.  
  
Thanks for the clarification. I forgot the ADL rules and just copy/pasted and tweaked the [example in the documentation for `std::complex`](https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/dom/conversion.html#json.dom.conversion.tag_invoke_overloads) but unfortunately the example omits the necessary namespaces.

---

## Comment 3

> Username: grisumbras  
> Created at: 2021-06-26 18:18:40 UTC  
> Url: https://github.com/boostorg/json/issues/466#issuecomment-869042230  

Since #574 was merged, this is proper fixed.
