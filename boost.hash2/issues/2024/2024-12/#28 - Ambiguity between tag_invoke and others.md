# #28 - Ambiguity between tag_invoke and others [Open]

> Username: Lastique  
> Created at: 2024-12-09 00:38:42 UTC  
> Updated at: 2024-12-09 02:53:07 UTC  
> Url: https://github.com/boostorg/hash2/issues/28  

It looks like the user-provided tag_invoke overload creates ambiguity if the type also matches other categories of types supported by Boost.Hash2. For example:  
  
```  
#include <iostream>  
#include <boost/describe.hpp>  
#include <boost/hash2/fnv1a.hpp>  
#include <boost/hash2/hash_append.hpp>  
  
struct X  
{  
    int a;  
  
    template< typename Hash, typename Flavor >  
    friend void tag_invoke(boost::hash2::hash_append_tag const&,  
        Hash& h, Flavor const& f, X const& v)  
    {  
        std::cout << "Called my tag_invoke" << std::endl;  
        boost::hash2::hash_append(h, f, v.a);  
    }  
};  
  
BOOST_DESCRIBE_STRUCT(X, (), (a))  
  
int main()  
{  
    boost::hash2::fnv1a_32 h1;  
    X v1 = { 1 };  
    boost::hash2::hash_append( h1, {}, v1 );  
}  
```  
  
Gcc 13.2 output:  
  
```  
In file included from test_tag_invoke.cpp:4:  
./include/boost/hash2/hash_append.hpp: In instantiation of ‘constexpr void boost::hash2::hash_append(Hash&, const Flavor&, const T&) [with Hash = fnv1a_32; Flavor = default_flavor; T = X]’:  
test_tag_invoke.cpp:25:30:   required from here  
./include/boost/hash2/hash_append.hpp:419:31: error: call of overloaded ‘do_hash_append(boost::hash2::fnv1a_32&, const boost::hash2::default_flavor&, const X&)’ is ambiguous  
  419 |         detail::do_hash_append( h, f, v );  
      |         ~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~  
./include/boost/hash2/hash_append.hpp:357:5: note: candidate: ‘constexpr typename std::enable_if<boost::container_hash::is_described_class<T>::value, void>::type boost::hash2::detail::do_hash_append(Hash&, const Flavor&, const T&) [with Hash = boost::hash2::fnv1a_32; Flavor = boost::hash2::default_flavor; T = X; typename std::enable_if<boost::container_hash::is_described_class<T>::value, void>::type = void]’  
  357 |     do_hash_append( Hash& h, Flavor const& f, T const& v )  
      |     ^~~~~~~~~~~~~~  
./include/boost/hash2/hash_append.hpp:401:5: note: candidate: ‘constexpr typename std::enable_if<boost::hash2::detail::has_tag_invoke<T>::value, void>::type boost::hash2::detail::do_hash_append(Hash&, const Flavor&, const T&) [with Hash = boost::hash2::fnv1a_32; Flavor = boost::hash2::default_flavor; T = X; typename std::enable_if<has_tag_invoke<T>::value, void>::type = void]’  
  401 |     do_hash_append( Hash& h, Flavor const& f, T const& v )  
      |     ^~~~~~~~~~~~~~  
```  
  
I think, a user-provided `tag_invoke` should always be preferred even if the type matches one of the natively supported type category. The code above is expected to compile and output "Called my tag_invoke".

---

## Comment 1

> Username: pdimov  
> Created at: 2024-12-09 01:06:28 UTC  
> Url: https://github.com/boostorg/hash2/issues/28#issuecomment-2526573812  

> I think, a user-provided tag_invoke should always be preferred even if the type matches one of the natively supported type category.  
  
That's the usual practice and was what I was going to implement, but as it turns out, when a type matches more than one category, this is often an error and you almost always want to know about it.  
  
The most recent such occurrence I encountered, for example, was when I defined `tag_invoke` for `boost::json::value`, without realizing that `json::value` implicitly constructs from `json::string`, `json::array`, and `json::object`, and the `tag_invoke` overload I wrote also matches these three, which was definitely not intended and would have caused infinite recursion at runtime.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-12-09 02:24:16 UTC  
> Updated at: 2024-12-09 02:26:15 UTC  
> Url: https://github.com/boostorg/hash2/issues/28#issuecomment-2526666754  

I think, `tag_invoke` should win in the example above because the user explicitly provided it for the purpose of hashing while the Boost.Describe description could be used for arbitrary purpose. The implementation of hashing may not be equivalent to the Describe description. For example, `tag_invoke` may exclude some members not intended for hashing.  
  
> The most recent such occurrence I encountered, for example, was when I defined `tag_invoke` for `boost::json::value`, without realizing that `json::value` implicitly constructs from `json::string`, `json::array`, and `json::object`, and the `tag_invoke` overload I wrote also matches these three, which was definitely not intended and would have caused infinite recursion at runtime.  
  
I think this sort of pitfall needs to be described in the docs, with the recommended best practices to avoid it (presumably, by prohibiting implicit conversions in the `tag_invoke` call through SFINAE). However, this does not mean that the use case in the bug description is invalid and should not work.  
  
BTW, I wonder if the `json::value` case could be protected against on the `hash_append` side somehow like [this](https://godbolt.org/z/n6ToY7EKW). The idea is that `ref_wrapper` is convertible to `T const&`, which should prevent the implicit construction of `value` from it, but should still allow the `tag_invoke` overload taking `value` to be picked. This won't work with something like `std::any`, which is constructible from pretty much anything, so maybe this implementation should be only enabled if `T` is *not* constructible from `ref_wrapper<T>`.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-12-09 02:53:06 UTC  
> Url: https://github.com/boostorg/hash2/issues/28#issuecomment-2526696866  

I agree that it should win, if we can assume that everything is written correctly, but until I/we figure out a way to make these mistakes less likely to occur, I'll leave it as is.  
  
The current workaround for the example above is to specialize is_described_class to false.
