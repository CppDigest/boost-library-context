# #175 - Policy for returning sequences of references [Open]

> Username: ldionne  
> Created at: 2015-09-08 19:24:59 UTC  
> Updated at: 2016-02-26 20:19:04 UTC  
> Url: https://github.com/boostorg/hana/issues/175  

This issue is a spinoff of #90. Here, instead of considering algorithms that return values/references like `at` and `at_key`, we consider algorithms that return containers that should probably hold references like `find_if` and `members`. I'm splitting #90 into two issues because I think the solution for both will be slightly different, even though related.  
  
To illustrate the issue, consider the following code, which was submitted to me in a private email:  
  
``` c++  
#include <boost/hana.hpp>  
#include <iostream>  
using namespace boost::hana;  
using namespace boost::hana::literals;  
  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (int, age)  
    );  
};  
  
int main() {  
   Person john{30};  
   members(john)[0_c] = 0;  
   // fails because the expression is not assignable  
}  
```  
  
This is because `members` returns a container holding copies of the members of the struct, not references to it. While it seems that it would be more useful to return a sequence of references, it would also create lifetime issues when the `Person` is a temporary.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-08 19:37:31 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-138678636  

Reading again my initial concerns about creating dangling references, I think it is misplaced to have this concern in the context of C++. I think returning a tuple of references is fine as long as  
1. it is documented (obviously), along with potential lifetime issues  
2. we don't return a tuple of references when called on a temporary object  
  
Hence, I have the following resolution in mind:  
  
| Argument of `members` | result type |  
| --- | --- |  
| `Object const&` | `tuple<Members const&...>` |  
| `Object&` | `tuple<Members&...>` |  
| `Object&&` | `tuple<Members...>` |  
  
The reasoning here is that when `members` is called on a non-temporary, we should return essentially a view over the existing members, so we would return a sequence of references. However, when we're called on a temporary object, we should move the members of the temporary into a new tuple holding them by value. Does that make sense?  
  
Now, this reasoning applies to other functions that behave similarly. Right now, all the functions I can think of are:  
- [ ] `members`, which returns a `Sequence` of (maybe) references  
- [ ] `find_if`, which returns an `optional`  
- [ ] `find`, which returns an `optional`  
- [ ] `keys`, which returns a `Sequence` of the keys in a map  
- [ ] `values`, which returns a `Sequence` of the values in a map  
  
It is likely that settling on a solution for `members` will also settle us for all of these other functions.

---

## Comment 2

> Username: badair  
> Created at: 2016-02-03 06:30:04 UTC  
> Updated at: 2016-02-03 06:32:20 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-179037811  

To quote you from #90, regarding your snippet there:  
  
> This is annoying, because from using auto m = ..., it would seem like you did a copy.  
  
I don't think that's annoying at all. I think it makes sense. If you want to copy members of a struct, just copy the struct. I think it's completely intuitive to say that `members` (et al.) return values provide references to the original object. The risk of reflecting over a dead object is greatly overshadowed by the benefits of member references of a live object, from an imperative perspective.  
  
> The reasoning here is that when members is called on a non-temporary, we should return essentially a view over the existing members, so we would return a sequence of references.  
  
I agree.  
  
> However, when we're called on a temporary object, we should move the members of the temporary into a new tuple holding them by value.  
  
For similar reasons as my last comment in #90, I tentatively argue that `members` called on a temporary object should return rvalue references to members, which lets the user determines whether a move is appropriate. I think it might be also unintuitive for lvalues to provide references while prvalues provide new values.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2016-02-03 21:58:01 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-179491485  

The `members` function could take an optional, second callback argument that behaves like unpack.  
I also threw in a `member_refs` convenience function to return a tuple of refs.  
  
``` cpp  
#include <boost/hana.hpp>  
#include <functional>  
#include <utility>  
  
namespace hana = boost::hana;  
using namespace boost::hana::literals;  
  
struct forward_as_tuple_t {  
  template<typename ...T>  
  constexpr auto operator()(T&&... t) const {  
    return hana::make_tuple(std::ref(std::forward<T>(t))...);  
  }  
};  
constexpr forward_as_tuple_t forward_as_tuple{};  
  
struct members_t {  
  template <typename S>  
  constexpr auto operator()(S&& s) const {  
    return hana::members(std::forward<S>(s));  
  }  
  
  template <typename S, typename Fn>  
  constexpr auto operator()(S&& s, Fn&& fn) const {  
    return hana::unpack(hana::accessors<std::decay_t<S>>(),  
      [&](auto&&... x) {  
        return fn(  
          hana::second(std::forward<decltype(x)>(x))(s)...  
        );  
      }  
    );  
  }  
};  
constexpr members_t members{};  
  
constexpr auto member_refs = hana::reverse_partial(members, ::forward_as_tuple);  
  
struct Person {  
  BOOST_HANA_DEFINE_STRUCT(Person,  
    (int, age)  
  );  
};  
  
int main() {  
  Person john{30};  
  
  members(john, [](auto& age) { age = 35; });  
  BOOST_HANA_RUNTIME_ASSERT(john.age == 35);  
  
  auto refs = member_refs(john);  
  refs[0_c].get() = 40;  
  BOOST_HANA_RUNTIME_ASSERT(john.age == 40);  
}  
```  
  
This would preserve the existing interface, and those using `member_refs` would know what they were getting into.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-02-07 19:28:25 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-181090115  

> The members function could take an optional, second callback argument that behaves like unpack.  
  
Well, the issue of returning sequences that contain references does not only apply to `members`. It also applies to functions like `find_if`, `find`, `keys`, etc... Hence, I think we need a proper solution instead of a workaround using a callback.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2016-02-07 19:40:42 UTC  
> Updated at: 2016-02-07 19:41:16 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-181092277  

@ldionne That's fine, but the idea is that they could all have an optional callback argument.  
  
Other than that perhaps functions like `find_key`, `find_index` so the user can use `at_key` or `at` if they need a reference. (ie instead of `find_if` directly)

---

## Comment 6

> Username: martinitus  
> Created at: 2016-02-25 13:26:11 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-188784116  

I came to a similar issue with `hana::zip` illustrated by the following code.  I'm comletely new to hana, and I think my intentions are clear from the code sample. I was quite happy that the commented line actually does NOT compile :+1:. However I did not find a nice workaround (Is there one?). Maybe something like `zip_as_ref`... Btw: I "wasted" half a day yesterday, reading through your `Monad` docu :D really awesome work you did here!  
HtH Martin  
  
``` c++  
#include <boost/hana.hpp>  
#include <iostream>  
namespace hana = boost::hana;  
  
int main(int argc, const char** argv)  
{  
    auto xs = hana::make<hana::tuple_tag>(1, 2, 3);  
    auto ys = hana::make<hana::tuple_tag>(1, 2, 3);  
    auto add_ref    = [](auto& x, const auto& y){x+=y;};  // well formed lambda  
    auto add_refref = [](auto&& x, const auto& y){x+=y;}; // ill formed lambda required for working solution  
    auto ref = [](auto& x) {return std::ref(x);};  
    auto print = [](const auto& z){ std::cout << z << std::endl;};  
  
    // supposed to add values of ys ontop of xs  
    hana::for_each(hana::zip(xs,ys),hana::fuse(add_refref));  
    // Fails: prints 1 2 3 because the x that ends up in add_refref is a temporary.  
    hana::for_each(xs,print);  
  
    // does not compile because x will be a temporary.  
    // at least the compiler warns us that something is fishy  
    //  candidate function [with $0 = int, $1 = int] not viable: expects an l-value for 1st argument  
    // hana::for_each(hana::zip(xs,ys),hana::fuse(add_ref));  
  
    // working solution but imo ugly  
    hana::for_each(hana::zip(hana::transform(xs,ref),ys),hana::fuse(add_refref));  
    hana::for_each(xs,print); // prints 2 4 6  
  
    // does not compile since x is still a temporary reference wrapper  
    // candidate function [with $0 = std::__1::reference_wrapper<int>, $1 = int] not viable: expects an l-value for 1st argument  
    // hana::for_each(hana::zip(hana::transform(xs,ref),ys),hana::fuse(add_ref));  
}  
```

---

## Comment 7

> Username: ricejasonf  
> Created at: 2016-02-25 19:17:44 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-188937812  

@aolsux `hana::at` returns a reference to the tuple's member at the provided index.  
  
``` cpp  
#include<boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
int main() {  
    auto xs = hana::make_tuple(1, 2, 3);  
    auto ys = hana::make_tuple(1, 2, 3);  
    hana::for_each(hana::range_c<int, 0, hana::length(xs)>,  
        [&](auto i) {  
            hana::at(xs, i) += hana::at(ys, i);  
        });  
  
    BOOST_HANA_RUNTIME_ASSERT(xs == hana::make_tuple(2, 4, 6));  
}  
```

---

## Comment 8

> Username: martinitus  
> Created at: 2016-02-26 14:55:05 UTC  
> Updated at: 2016-02-26 14:56:06 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-189312548  

@ricejasonf yeah, that is more or less equal to the code based on my own small meta library which i wanted to replace by hana :D maybe if just written to much python code these days...  
Still i think its worth considering `hana::zip` in the context of functions returning sequences of references! Even if it would return a sequence of values holding reference.

---

## Comment 9

> Username: ricejasonf  
> Created at: 2016-02-26 17:43:07 UTC  
> Updated at: 2016-02-26 17:45:52 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-189387415  

How about a `hana::forward_as` function that has specializations for functions where the resulting structure contains the reference wrapped results? I would suggest an alias `hana::forward` for search operations, but that name would be kind of confusing. idk.  
  
``` cpp  
#include <boost/hana.hpp>  
#include <memory>  
#include <utility>  
namespace hana = boost::hana;  
using namespace boost::hana::literals;  
  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (int, age)  
    );  
};  
  
// this would actually be an impl for `hana::members`  
struct forward_as_t {  
    template<typename S>  
    constexpr auto operator()(hana::members_t const&, S&& s) const {  
        return hana::unpack(hana::accessors<std::decay_t<S>>(),  
            [&](auto&&... x) {  
                return hana::make_tuple(  
                    std::ref(hana::second(std::forward<decltype(x)>(x))(s))...  
                );  
            }  
        );  
    }  
};  
constexpr forward_as_t forward_as{};  
constexpr auto forward_as_members = hana::partial(forward_as, hana::members);  
// or maybe this way  
// constexpr forward_as<hana::members_t> forward_as_members{};  
  
int main() {  
   Person john{30};  
  
   forward_as(hana::members, john)[0_c].get() = 35;  
   BOOST_HANA_RUNTIME_ASSERT(john.age == 35);  
  
   forward_as_members(john)[0_c].get() = 40;  
   BOOST_HANA_RUNTIME_ASSERT(john.age == 40);  
}  
```

---

## Comment 10

> Username: ldionne  
> Created at: 2016-02-26 19:54:39 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-189455146  

@aolsux  
  
If `hana::zip` was to return a sequence of sequences holding references to the original members, the value semantics used throughout Hana wouldn't be respected:  
  
``` c++  
auto ts = hana::make_tuple(1, 2, 3);  
auto us = hana::make_tuple(4, 5, 6);  
  
auto z = hana::zip(ts, us); // z does not 'own' its elements, which is not consistent  
```  
  
While I very much understand the interest of being able to retain references, it's not the way Hana was designed. Instead, if you need references, I would do it explicitly:  
  
``` c++  
auto xs = hana::make_tuple(1, 2, 3);  
auto ys = hana::make_tuple(1, 2, 3);  
auto ref = [](auto& x) { return std::ref(x); };  
  
hana::for_each(hana::zip(hana::transform(xs, ref), ys), [](auto x, auto const& y) {  
    x.get() += y;  
});  
```  
  
It does require the additional steps of `hana::transform(xs, ref)` and `x.get()`, but it also makes things more explicit (which is not necessarily bad) and it's not incredibly complicated to write. Also, you can see [this comment](https://github.com/boostorg/hana/pull/179#issuecomment-139915710) for a partial motivation for disallowing sequences of references altogether.  
  
I do agree that the ability to form sequences of references is useful, and it has been requested many times. However, I think that we stand a better chance of doing this properly if we introduce something like a `zipped_view`, which would make it obvious that we're dealing with data owned somewhere else (since it's a view).

---

## Comment 11

> Username: ldionne  
> Created at: 2016-02-26 20:19:04 UTC  
> Url: https://github.com/boostorg/hana/issues/175#issuecomment-189465299  

Actually, what about writing the following?  
  
``` c++  
auto xs = hana::make_tuple(1, 2, 3);  
auto ys = hana::make_tuple(1, 2, 3);  
  
xs = hana::zip_with(hana::plus, xs, ys);  
```  
  
I'd be tempted to think that at least in simple cases, the optimizer would be able to do a good job and not move ints around.
