# #331 - "No matching call to `ebo_get`" regression [Closed]

> Username: vittorioromeo  
> Created at: 2017-04-08 12:12:20 UTC  
> Updated at: 2017-06-06 16:51:12 UTC  
> Closed at: 2017-06-06 16:51:12 UTC  
> Url: https://github.com/boostorg/hana/issues/331  

Consider the following code:  
  
```cpp  
#include <boost/hana.hpp>  
  
namespace bh = boost::hana;  
  
int main()  
{  
    auto m = bh::make_map();  
    bh::int_<0> key;  
    bh::int_<1> x;  
    auto new_map =  
        bh::insert(m, bh::make_pair(key, bh::make_pair(x, bh::false_c)));  
}  
```  
  
It compiles and works properly with the last version of Boost. [**on wandbox**](https://wandbox.org/permlink/F80HpydVhAwQt5hX).  
  
With the `master` branch of this repository, the code fails to compile:  
  
>   
> In file included from /usr/local/include/boost/hana/detail/struct_macros.hpp:27:0,  
>                  from /usr/local/include/boost/hana/adapt_adt.hpp:15,  
>                  from /usr/local/include/boost/hana.hpp:59,  
>                  from hanabug.cpp:1:  
> /usr/local/include/boost/hana/pair.hpp: In instantiation of ‘static constexpr decltype(auto) boost::hana::first_impl<boost::hana::pair_tag>::apply(P&&) [with P = boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >&]’:  
> /usr/local/include/boost/hana/first.hpp:34:28:   required from ‘constexpr decltype(auto) boost::hana::first_t::operator()(Pair&&) const [with Pair = boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >&]’  
> /usr/local/include/boost/hana/map.hpp:307:45:   required from ‘static constexpr auto boost::hana::insert_impl<boost::hana::map_tag>::apply(Map&&, Pair&&) [with Map = boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&; Pair = boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >]’  
> /usr/local/include/boost/hana/insert.hpp:28:68:   required from ‘constexpr decltype(auto) boost::hana::insert_t::operator()(Set&&, Args&& ...) const [with Set = boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> >&; Args = {boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >}]’  
> hanabug.cpp:10:83:   required from here  
> /usr/local/include/boost/hana/pair.hpp:166:49: error: no matching function for call to ‘ebo_get<boost::hana::detail::pix<0> >(boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >&)’  
>          { return detail::ebo_get<detail::pix<0>>(static_cast<P&&>(p)); }  
>                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
> In file included from /usr/local/include/boost/hana/pair.hpp:17:0,  
>                  from /usr/local/include/boost/hana/detail/struct_macros.hpp:27,  
>                  from /usr/local/include/boost/hana/adapt_adt.hpp:15,  
>                  from /usr/local/include/boost/hana.hpp:59,  
>                  from hanabug.cpp:1:  
>   
  
---  
  
Related ECST issue [#30](https://github.com/SuperV1234/ecst/issues/30).

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-04-08 16:44:43 UTC  
> Updated at: 2017-04-08 16:45:35 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292729787  

With clang 3.9, I get a different error message:  
```  
/usr/local/src/hana/include/boost/hana/pair.hpp:93:50: error: cannot cast from lvalue of type  
      'boost::hana::integral_constant<int, 1>' to rvalue reference type  
      'boost::hana::integral_constant<int, 0> &&'; types are not compatible  
            : detail::ebo<detail::pix<0>, First>(static_cast<T&&>(detail::ebo_get<detail::pix<0>>(other)))  
                                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2017-04-08 16:47:05 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292729987  

I have reduced it to  
  
```c++  
#include <boost/hana/bool.hpp>  
#include <boost/hana/first.hpp>  
#include <boost/hana/integral_constant.hpp>  
#include <boost/hana/pair.hpp>  
#include <boost/hana/second.hpp>  
  
#include <type_traits>  
namespace hana = boost::hana;  
  
  
int main() {  
    using Pair = hana::pair<hana::int_<0>, hana::pair<hana::int_<1>, hana::false_>>;  
    Pair pair{};  
  
    auto a = hana::first(static_cast<Pair&&>(pair));  
    static_assert(std::is_same<decltype(a), hana::int_<0>>{}, "");  
  
    auto b = hana::second(static_cast<Pair&&>(pair));  
    static_assert(std::is_same<decltype(b), hana::pair<hana::int_<1>, hana::false_>>{}, "");  
}  
```

---

## Comment 3

> Username: ldionne  
> Created at: 2017-04-08 16:48:05 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292730078  

IOW, it's not getting the correct member in case of nested pairs. This is most likely due to a strange interaction between base classes, but I've yet to understand it. Working on it now.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-04-08 17:00:40 UTC  
> Updated at: 2017-04-08 17:01:15 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292731170  

This should also apply to `basic_tuple` because they both are implemented the same, but `basic_tuple` is marked as `final`, so we end up not having this problem (but also not getting full EBO for nested empty tuples). Still trying to understand which base class should be picked and what the correct fix is.

---

## Comment 5

> Username: ldionne  
> Created at: 2017-04-08 17:55:46 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292734727  

When #332 passes the CI tests, I'll merge those commits to master too so they are included in Boost 1.64.

---

## Comment 6

> Username: ldionne  
> Created at: 2017-04-08 21:12:38 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292745985  

OK. The bug is fixed on `develop` by #332, and I've asked permission to do a post-beta merge to `master` so that Boost 1.64 gets the fix [here](http://boost.2283326.n4.nabble.com/release-1-64-0-post-beta-merges-tp4692872p4693545.html).

---

## Comment 7

> Username: vittorioromeo  
> Created at: 2017-04-09 10:57:42 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-292778677  

Awesome, thanks.

---

## Comment 8

> Username: vittorioromeo  
> Created at: 2017-05-27 14:26:42 UTC  
> Updated at: 2017-05-30 15:05:23 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-304455076  

@ldionne It seems like my original snippet still fails to compile on `develop`:  
  
```cpp  
#include <boost/hana.hpp>  
namespace bh = boost::hana;  
  
int main()  
{  
    auto m = bh::make_map();  
    bh::int_<0> key;  
    bh::int_<1> x;  
    auto new_map =  
        bh::insert(m, bh::make_pair(key, bh::make_pair(x, bh::false_c)));  
}  
```  
  
Error:  
  
```  
In file included from hanabug2.cpp:1:  
In file included from /usr/local/include/boost/hana.hpp:59:  
In file included from /usr/local/include/boost/hana/adapt_adt.hpp:15:  
In file included from /usr/local/include/boost/hana/detail/struct_macros.hpp:27:  
/usr/local/include/boost/hana/pair.hpp:93:67: error: no matching function for call to 'ebo_get'  
            : detail::ebo<detail::pix<0>, First>(static_cast<T&&>(detail::ebo_get<detail::pix<0>>(other)))  
                                                                  ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/hana/detail/ebo.hpp:62:15: note: in instantiation of function template specialization 'boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int,  
      1>, boost::hana::integral_constant<bool, false> >, void>' requested here  
            : V(static_cast<T&&>(t))  
              ^  
/usr/local/include/boost/hana/basic_tuple.hpp:62:19: note: in instantiation of function template specialization '_hana::ebo<boost::hana::detail::bti<0>, boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >, true>::ebo<boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
                : detail::ebo<bti<n>, Xn>(static_cast<Yn&&>(yn))...  
                  ^  
/usr/local/include/boost/hana/basic_tuple.hpp:89:15: note: in instantiation of function template specialization 'boost::hana::detail::basic_tuple_impl<std::integer_sequence<unsigned long, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > >  
      >::basic_tuple_impl<boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
            : Base(static_cast<Yn&&>(yn)...)  
              ^  
/usr/local/include/boost/hana/basic_tuple.hpp:238:20: note: in instantiation of function template specialization 'boost::hana::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >::basic_tuple<boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
            return basic_tuple<typename detail::decay<Xn>::type...>{  
                   ^  
/usr/local/include/boost/hana/fwd/core/make.hpp:61:36: note: in instantiation of function template specialization 'boost::hana::make_impl<boost::hana::basic_tuple_tag,  
      void>::apply<boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
            return make_impl<Tag>::apply(static_cast<X&&>(x)...);  
                                   ^  
/usr/local/include/boost/hana/append.hpp:60:20: note: (skipping 2 contexts in backtrace; use -ftemplate-backtrace-limit=0 to see all)  
            return hana::make<S>(  
                   ^  
/usr/local/include/boost/hana/append.hpp:43:24: note: in instantiation of function template specialization 'boost::hana::append_impl<boost::hana::basic_tuple_tag, boost::hana::when<true>  
      >::apply<boost::hana::basic_tuple<> &, boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >'  
      requested here  
        return Append::apply(static_cast<Xs&&>(xs), static_cast<X&&>(x));  
                       ^  
/usr/local/include/boost/hana/map.hpp:284:17: note: in instantiation of function template specialization 'boost::hana::append_t::operator()<boost::hana::basic_tuple<> &,  
      boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
                hana::append(static_cast<Map&&>(map).storage, static_cast<Pair&&>(pair))  
                ^  
/usr/local/include/boost/hana/map.hpp:312:20: note: in instantiation of function template specialization 'boost::hana::insert_impl<boost::hana::map_tag,  
      void>::helper<boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > &, boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
            return helper(static_cast<Map&&>(map), static_cast<Pair&&>(pair), MaybeIndex{});  
                   ^  
/usr/local/include/boost/hana/insert.hpp:28:63: note: in instantiation of function template specialization 'boost::hana::insert_impl<boost::hana::map_tag,  
      void>::apply<boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > &, boost::hana::pair<boost::hana::integral_constant<int, 0>,  
      boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
        return insert_impl<typename hana::tag_of<Set>::type>::apply(  
                                                              ^  
hanabug2.cpp:11:19: note: in instantiation of function template specialization 'boost::hana::insert_t::operator()<boost::hana::detail::map_impl<boost::hana::detail::hash_table<>, boost::hana::basic_tuple<> > &,  
      boost::hana::pair<boost::hana::integral_constant<int, 0>, boost::hana::pair<boost::hana::integral_constant<int, 1>, boost::hana::integral_constant<bool, false> > > >' requested here  
        bh::insert(m, bh::make_pair(key, bh::make_pair(x, bh::false_c)));  
                  ^  
/usr/local/include/boost/hana/detail/ebo.hpp:83:24: note: candidate template ignored: failed template argument deduction  
    constexpr V const& ebo_get(ebo<K, V, true> const& x)  
                       ^  
/usr/local/include/boost/hana/detail/ebo.hpp:87:18: note: candidate template ignored: failed template argument deduction  
    constexpr V& ebo_get(ebo<K, V, true>& x)  
                 ^  
/usr/local/include/boost/hana/detail/ebo.hpp:91:19: note: candidate template ignored: failed template argument deduction  
    constexpr V&& ebo_get(ebo<K, V, true>&& x)  
                  ^  
/usr/local/include/boost/hana/detail/ebo.hpp:96:24: note: candidate template ignored: could not match 'ebo' against 'pair'  
    constexpr V const& ebo_get(ebo<K, V, false> const& x)  
                       ^  
/usr/local/include/boost/hana/detail/ebo.hpp:100:18: note: candidate template ignored: could not match 'ebo' against 'pair'  
    constexpr V& ebo_get(ebo<K, V, false>& x)  
                 ^  
/usr/local/include/boost/hana/detail/ebo.hpp:104:19: note: candidate template ignored: could not match 'ebo' against 'pair'  
    constexpr V&& ebo_get(ebo<K, V, false>&& x)  
                  ^  
In file included from hanabug2.cpp:1:  
In file included from /usr/local/include/boost/hana.hpp:59:  
In file included from /usr/local/include/boost/hana/adapt_adt.hpp:15:  
In file included from /usr/local/include/boost/hana/detail/struct_macros.hpp:27:  
/usr/local/include/boost/hana/pair.hpp:94:68: error: no matching function for call to 'ebo_get'  
            , detail::ebo<detail::pix<1>, Second>(static_cast<U&&>(detail::ebo_get<detail::pix<1>>(other)))  
                                                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/hana/detail/ebo.hpp:83:24: note: candidate template ignored: failed template argument deduction  
    constexpr V const& ebo_get(ebo<K, V, true> const& x)  
                       ^  
/usr/local/include/boost/hana/detail/ebo.hpp:87:18: note: candidate template ignored: failed template argument deduction  
    constexpr V& ebo_get(ebo<K, V, true>& x)  
                 ^  
/usr/local/include/boost/hana/detail/ebo.hpp:91:19: note: candidate template ignored: failed template argument deduction  
    constexpr V&& ebo_get(ebo<K, V, true>&& x)  
                  ^  
/usr/local/include/boost/hana/detail/ebo.hpp:96:24: note: candidate template ignored: could not match 'ebo' against 'pair'  
    constexpr V const& ebo_get(ebo<K, V, false> const& x)  
                       ^  
/usr/local/include/boost/hana/detail/ebo.hpp:100:18: note: candidate template ignored: could not match 'ebo' against 'pair'  
    constexpr V& ebo_get(ebo<K, V, false>& x)  
                 ^  
/usr/local/include/boost/hana/detail/ebo.hpp:104:19: note: candidate template ignored: could not match 'ebo' against 'pair'  
    constexpr V&& ebo_get(ebo<K, V, false>&& x)  
                  ^  
2 errors generated.  
  
```  
  
With colors:  
  
```bash  
curl http://termbin.com/ut6o   
````

---

## Comment 9

> Username: ldionne  
> Created at: 2017-06-06 16:51:12 UTC  
> Url: https://github.com/boostorg/hana/issues/331#issuecomment-306548364  

Should be fixed in develop, closing.
