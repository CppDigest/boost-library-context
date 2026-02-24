# #367 - [SetTheoretic] Minimal Representation via Accessors [Open]

> Username: ricejasonf  
> Created at: 2017-09-11 21:57:34 UTC  
> Updated at: 2018-01-02 00:23:01 UTC  
> Url: https://github.com/boostorg/hana/issues/367  

Consider the following minimal representation for `SetTheoretical`:  
  
Refines `hana::Foldable` (unless we want infinite sets???)  
  
Implements `get<T>(s)` where T is like a key for an element.  
  
Implements `key_of<S>(e)` where `e` is an element belonging to a set of tag `S`  
  
Implements `make<S>` (or both `insert` and `erase_key`)  
  
Then implementations of the currently proposed  `SetTheoretical` concept such as `union_`, `intersection`, etc. would be provided by the above.  
  
For `Searchable` the only issue I still see is with providing an efficient `contains` from `get<T>`. Perhaps if it was somehow SFINAE friendly or it returned an optional or something.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-09-11 22:40:32 UTC  
> Updated at: 2017-09-13 17:17:35 UTC  
> Url: https://github.com/boostorg/hana/issues/367#issuecomment-328679170  

Here is a theoretical implementation of `union_` with a key difference.  
  
```    
    template <typename S>  
    struct union_impl<S, when<hana::SetTheoretical<S>::value>> {  
        template <typename Xs, typename Ys>  
        static constexpr auto apply(Xs&& xs, Ys&& ys) {  
            constexpr unpack_keys = [](auto const& ...x) {  
              return detail::keys<std::decay_t<decltype(hana::key_of<Tag>(x)...)>>{}  
            };  
            using Xkeys = decltype(hana::unpack(xs, unpack_keys));  
            using Ykeys = decltype(hana::unpack(ys, unpack_keys));  
            using Zkeys = detail::key_difference_t<Ykeys, XKeys>;  
  
            return detail::unpack_keys<Xkeys, ZKeys>(  
              [&](detail::keys<X...>, detail::keys<Y...>) {  
                return hana::make<hana::tag_of:t<Xs>>(hana::get<X>(xs)..., hana::get<Y>(ys));  
              });  
        }  
    };  
  
```  
  
This would be more efficient than using `insert` for every element in `Ys`.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2017-09-13 18:11:51 UTC  
> Url: https://github.com/boostorg/hana/issues/367#issuecomment-329251538  

Okay. So I am now convinced that adding those two functions to `Searchable` is the way to go except changing `get<T>` to something like `at_key_c<T>`.  
  
Then `SetTheoretical` could be provided by `Searchable` for free with significant optimizations over some of the current implementations. (e.g. fold with insert)  
  
FWIW I am trying to model changes in state as a delta-ring of sets in a project I am working on so if this does not make sense in Boost.Hana then I will just make it an implementation detail in my code. (specifically `key_of<S>`)  
  
I would like to make a PR to add `at_key_c<T>` and  `key_of<S>` to `Searchable`.  
  
ping @ldionne @shreyans800755

---

## Comment 3

> Username: ldionne  
> Created at: 2018-01-01 20:59:05 UTC  
> Url: https://github.com/boostorg/hana/issues/367#issuecomment-354676279  

Interesting. Can you explain why you need `at_key_c<T>` instead of using `at_key`?  
  
Also, just to clarify, `key_of<hana::set_tag>` would be the identity function, and `key_of<hana::map_tag>` would be a function that takes any `hana::Product` and returns its first element, right?

---

## Comment 4

> Username: ricejasonf  
> Created at: 2018-01-01 23:58:54 UTC  
> Updated at: 2018-01-02 00:12:40 UTC  
> Url: https://github.com/boostorg/hana/issues/367#issuecomment-354685978  

The case for `at_key_c` is that `at_key` uses equality comparison that must return a **compile-time** Logical, but the user is required to give an instance that might have run-time state and might not be default constructible.  
  
The following code should demonstrate, but it is not even compiling for me which is weird:  
  
```cpp  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
int main() {  
 auto xs = hana::make_tuple(float{5.6f}, int{42});  
  
 hana::at_key(xs, int{}); // explodes  
 hana::find(xs, int{}); // also explodes  
}  
```  
(It is also broken for me on a version of Hana before `at_key` was using the new `index_if` stuff.)  
  
The error is that the predicate is indeed returning a raw bool. I'm not sure how that is working in the tests.  
  
ug I will try to make a better example.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2018-01-02 00:22:08 UTC  
> Updated at: 2018-01-02 00:23:01 UTC  
> Url: https://github.com/boostorg/hana/issues/367#issuecomment-354687612  

Okay, I can see that `at_key_c` would not be necessary.  
  
> Also, just to clarify, `key_of<hana::set_tag>` would be the identity function, and `key_of<hana::map_tag>` would be a function that takes any `hana::Product` and returns its first  element, right?  
  
Yes and yes.
