# #317 - [Tuple] C++17 structured bindings with hana::tuple [Open]

> Username: tivek  
> Created at: 2017-01-11 15:34:16 UTC  
> Updated at: 2017-12-02 03:58:17 UTC  
> Url: https://github.com/boostorg/hana/issues/317  

C++17 introduces structured bindings, a way to initialize multiple separate variables using std::tuple, std::pair, or any "simple" class with all-public non-static data members.  
  
hana::tuple strives to provide an interface similar to std::tuple, so it would be useful to have it "just work" with the new structured bindings syntax. With Boost 1.63.0 the following example does not compile on clang version 4.0.0 (trunk 291432):  
  
```C++  
// compile with -std=c++1z  
#include <boost/hana/tuple.hpp>  
#include <tuple>  
  
int main()  
{  
    // std::tuple OK  
    auto [a, b, c] = std::make_tuple(1, 3.14, "Hello");  
      
    // hana::tuple errors out  
    auto [d, e, f] = boost::hana::make_tuple(1, 3.14, "Hello");  
    // error: type 'boost::hana::tuple<int, double, const char *>' decomposes into 1 elements, but 3 names were provided  
}  
```  
  
Is such an enhancement feasible?

---

## Comment 1

> Username: tivek  
> Created at: 2017-01-11 19:18:19 UTC  
> Url: https://github.com/boostorg/hana/issues/317#issuecomment-271966507  

A little followup. It seems the discussion about structured binding customization points is still open, see [P0326R0](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0326r0.pdf), but according to [Herb Sutter](https://herbsutter.com/2016/06/30/trip-report-summer-iso-c-standards-meeting-oulu/) it should be enough to specialize `std::tuple_element<n, hana::tuple>` and provide a`get<n>()`. In principle it should be possible to make similar extensions to any `Iterable`. Here is a quick example for `hana::tuple` ([Wandbox](http://melpon.org/wandbox/permlink/CD20bAarM4jzLpH2)):  
  
```C++  
#include <boost/hana/tuple.hpp>  
#include <boost/hana/integral_constant.hpp>  
#include <cstddef>  
#include <iostream>  
  
  
namespace std  
{  
    template<std::size_t n, typename... Types>  
    struct tuple_element<n, boost::hana::tuple<Types...>>  
    {  
        using type = typename decltype(+boost::hana::tuple_t<Types...>[boost::hana::size_c<n>])::type;  
    };  
      
    template<typename... Types>  
    struct tuple_size<boost::hana::tuple<Types...>>:  
        public integral_constant<std::size_t, sizeof...(Types)>  
    {};  
}  
  
namespace boost  
{  
    namespace hana  
    {  
        template<std::size_t n, typename... Types>  
        constexpr decltype(auto) get(hana::tuple<Types...>& t)  
        {  
            return t[hana::size_c<n>];  
        }  
          
        template<std::size_t n, typename... Types>  
        constexpr decltype(auto) get(const hana::tuple<Types...>& t)  
        {  
            return t[hana::size_c<n>];  
        }  
  
        template<std::size_t n, typename... Types>  
        constexpr decltype(auto) get(hana::tuple<Types...>&& t)  
        {  
            return static_cast<hana::tuple<Types...>&&>(t)[hana::size_c<n>];  
        }  
          
        template<std::size_t n, typename... Types>  
        constexpr decltype(auto) get(const hana::tuple<Types...>&& t)  
        {  
            return static_cast<const hana::tuple<Types...>&&>(t)[hana::size_c<n>];  
        }  
    }  
}  
  
  
int main()  
{  
    auto t = boost::hana::make_tuple(1, 3.14, "Hello");  
    auto& [a, b, c] = t;  
    std::cout << b << std::endl; // yay  
}  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2017-01-11 22:46:35 UTC  
> Url: https://github.com/boostorg/hana/issues/317#issuecomment-272020200  

That's pretty neat! Yes, when C++17 hits us I'll be happy to add this feature. It's clear how that should work for `tuple` and `pair`, but what about `set`, `map`, `optional` & al? Should we also try to do something?

---

## Comment 3

> Username: tivek  
> Created at: 2017-01-12 00:09:43 UTC  
> Updated at: 2017-01-12 00:29:31 UTC  
> Url: https://github.com/boostorg/hana/issues/317#issuecomment-272036496  

Structured binding essentially means using a linearization to initialize variables element by element, so `Foldable`s in general are definitely worth a thought.  
  
One issue comes to mind with unrestricted `Foldable`s. The variables being declared are named and ordered in the code by hand. Most likely the intent is "I know what these will be and want to give them meaningful names". However, the `Foldable` we are structurally binding to does not have to produce its linearization in any specific order. This sounds like a mismatch. How about requiring finite, enumerable `Sequence`s instead of naked `Foldable`s? Would that be less surprising and still useful enough?

---

## Comment 4

> Username: ldionne  
> Created at: 2017-12-02 03:58:13 UTC  
> Url: https://github.com/boostorg/hana/issues/317#issuecomment-348666008  

I'm marking this for Hana 2.0, which will most likely be a C++17/20 rewrite of Hana.
