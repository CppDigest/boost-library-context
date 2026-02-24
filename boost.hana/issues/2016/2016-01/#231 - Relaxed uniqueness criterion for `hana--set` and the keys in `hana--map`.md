# #231 - Relaxed uniqueness criterion for `hana::set` and the keys in `hana::map` [Closed]

> Username: m-j-w  
> Created at: 2016-01-03 18:40:10 UTC  
> Updated at: 2016-01-05 13:44:09 UTC  
> Closed at: 2016-01-05 13:39:37 UTC  
> Url: https://github.com/boostorg/hana/issues/231  

I propose the relaxation of the uniqueness requirement of `hana::set` and the keys in `hana::map` to not require equality comparability _between_ different elements or keys, respectively.  
Currently, `hana::equal` is required, which requires every type to be comparable with every other. It'll complain about intentionally not implementing a default for that, which I consider the correct approach to not add too much ambiguity.  
However, for `hana::set` and `hana::map` this seems to me too much. If two types differ, and if they don't implement an equality comparator, then it should be safe and correct to assume their not equal. Thus, the uniqueness criterion should be in some pseudo code something like:  
  
``` cpp  
constexpr bool equal_unique( T&& t, U&& u ) {  
    // Assume some SFINAE dispatching for the following  
    if ( implemented(t==u) ) return t==u;  
    if ( implemented(hana::equal<T,U>) return hana::equal<T,U>::value;  
    return std::is_same<T,U>::value;  // New fallback for uniqueness in hana::set and hana::map  
}  
```  
  
Of course, there is the issue of a type that is actually associated with a run-time value and whether that should be checked or not. But I think that is already covered by wrapping a type such as `int` as a `type<int>` for example, if that comparison is to be pursued on the type only.  
  
The whole point of this is simple: Consider a set or map with keys of an `int`, a `type<int>` and an `int_c<...>`. The differences are apparent: The first is some specific integer only known at run-time, the second is basically any integer, and the last is an integer always known at compile-time. And it should be possible to stick all three into one set or map and act accordingly. Thus, the comparison between types should not be a necessary requirement to ensure uniqueness. (Anyway, I'm not so sure whether this could or should actually be working, but I would have some use-cases for that...)  
  
This somehow relates to #25, although the comparison is not really user-defined.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-01-04 21:32:29 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-168816224  

I am a bit confused, because `hana::set` and `hana::map` do not explicitly require comparability _between_ elements or keys, respectively. I assume you are referring to the following lines of the documentation  
  
for `hana::set`:  
  
> Basic unordered container requiring compile-time `Comparable` elements.  
  
for `hana::map`:  
  
> Basic associative container requiring unique and `Comparable` keys.  
  
If so, then I agree that they could be made more precise. Also, I would like to point out that `equal` is _currently_ specified in the following way (roughly):  
  
``` c++  
auto equal(T t, U u) {  
    if (implemented(t == u))   
        return t == u;  
    else if (implemented(hana::equal_impl<T, U>))   
        return hana::equal_impl<T,U>::apply(t, u);  
    else if (T and U can be converted without loss to a common type C)  
        return equal(to<C>(t), to<C>(u));  
    else {  
        static_assert(T and U do not have a common type, "");  
        return false;  
    }  
}  
```  
  
Hence, the only way `equal` can produce an error is if we are comparing two types that can be converted **with loss of information** to a common type, but for which no comparison is provided. In all other cases, `equal` will not produce an error and will return something sensible.  
  
If the above does not answer your concerns, could you please provide a minimal (non-)working example showing what you would like to achieve, and how Hana _does not_ allow for it because of that too stringent requirement?

---

## Comment 2

> Username: m-j-w  
> Created at: 2016-01-04 21:57:14 UTC  
> Updated at: 2016-01-04 22:13:18 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-168823712  

Sure, here's the original example that led to that issue (and the one before):  
  
``` cpp  
#include <boost/hana.hpp>  
#include <boost/hana/experimental/printable.hpp>  
  
namespace hana = boost::hana;  
using namespace hana::literals;  
using namespace std::literals;  
using namespace hana::experimental;  
  
#include <iostream>  
  
int main() {  
  auto m0 = hana::make_map(  
      // Doesn't work, not hana-compile-time-comparable  
      //  hana::make_pair( 1, "1"s ),  
      //  hana::make_pair( 2, "2"s )  
      );  
  std::cout << print(m0) << '\n';  
  
  auto m1 = hana::make_map( hana::make_pair(1_c, "1"s),  
                            hana::make_pair(2_c, "2"s) );  
  std::cout << print(m1) << '\n';  
  
  struct key_1_t {} key_1; struct key_2_t {} key_2;  
  auto m2 = hana::make_map(  
      // Doesn't work since not comparable due to lacking a default comparator  
      //  hana::make_pair(key_1, "1"s),  
      //  hana::make_pair(key_2, "2"s)  
      );  
  std::cout << print(m2) << '\n';  
  auto m3 = hana::make_map(  
      hana::make_pair(hana::type_c<key_1_t>, "1"s),  
      hana::make_pair(hana::type_c<key_2_t>, "2"s)  
      );  
  std::cout << print(m3) << '\n';  
  auto m4 = hana::make_map(  
      // Results in a map having non-unique keys  
      hana::make_pair(hana::type_c<hana::tag_of_t<hana::int_<1>>>, "1"s),  
      hana::make_pair(hana::type_c<hana::tag_of_t<hana::int_<2>>>, "2"s)  
      );  
  std::cout << print(m4) << '\n';  
}  
```  
  
The difference between the present implementation of `equal` and the proposed relaxed version is simply the fallback using the `std::is_same<T,U>` to make the above work without having to add a user-defined equality comparator.  
And since that would be possibly irritating for the general `equal` comparison, I named that `equal_unique`, and propose to only use it for `hana::set` and keys of `hana::map`.

---

## Comment 3

> Username: ldionne  
> Created at: 2016-01-04 22:26:30 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-168830536  

Ah! Here's the reduced example (I thought this was a bug and I started writing a test for it):  
  
``` c++  
#include <boost/hana/experimental/printable.hpp>  
#include <boost/hana/map.hpp>  
#include <boost/hana/pair.hpp>  
#include <iostream>  
namespace hana = boost::hana;  
  
  
// This test makes sure that we can create a hana::map with keys that are not  
// Comparable with themselves. Before, this would trigger a static assertion  
// when creating the hana::map.  
//  
// Note that it makes sense to create such a map, because  
//  [TODO: well perhaps it does not makes much sense after all...]  
  
struct key_1 { };  
struct key_2 { };  
  
int main() {  
    auto m = hana::make_map(  
        hana::make_pair(key_1{}, 1),  
        hana::make_pair(key_2{}, 2)  
    );  
  
    std::cout << hana::experimental::print(m) << std::endl;  
}  
```  
  
What happens here is that `key_1{}` is not `Comparable` with **itself**, and this is caught when creating the `hana::map` because all keys `K` need to be `Comparable<K, K>` (hence `Comparable` with themselves). So the issue is not that `key_1` is not `Comparable` with `key_2` (because it is, and their comparison returns `hana::false_c`), but rather that `key_1` is not `Comparable` with `key_1` (and similarly for `key_2` with `key_2`).  
  
Now, as you can see in the above comment, I asked myself the following question: Does it make sense to create such a map in the first place? At first, my answer was yes, but as I think of it more and more I can't find a use case. In particular, you can't possibly access at any key of that `map`, because that key would have to be compared with itself, and the comparison would have to return true, for the associated value to be returned. But since the key can't be compared with itself, surely its associated value can never be accessed.  
  
My impression is that what you're really trying to do (conceptually) is to map C++ types to values. Indeed, instances of singleton types (`key_1{}`) are equivalent to the types themselves (`key_1`). However, you seem to want to "bypass" the way Hana works with types (`hana::type`) in favour of having builtin support for instances of singleton types.  
  
Does that make sense to you? Is there a reason why you're not using `hana::type`s?

---

## Comment 4

> Username: m-j-w  
> Created at: 2016-01-04 22:50:46 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-168836755  

It does make sense. But well, your analysis of my intentions is just partially correct: I was merely playing with possible ways to use `hana::map` with keys being tags, and was rather puzzled how to work around that static assert error of not being comparable. I recognized that if I'd just like to put hana tags as keys I would need to write an equality comparator for all of them, or would always have to wrap them as `type<xxx_tag>`. However, for a tag that doesn't really make sense...  
  
Suppose I have a whole bunch of user-defined types, along with an also rather large number of customized `hana_tag`s, I'd either have to put them into a `hana::type` as keys, or modify Hana itself by adding the `tag_tag` (hence the other issue #232, btw. no complaints about that being closed), or get rid of that `static_assert` warning in `hana::equal` to be able to add the `std::is_same` overload.  
  
The last two are not a real option, since tampering with the core of a library for my own purposes is a no-go; hence the issue.  
  
The same of course also applies to `hana::set` where one could imagine to collect some sub-set of tags.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-01-04 22:54:22 UTC  
> Updated at: 2016-01-04 22:55:13 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-168837494  

> However, for a tag that doesn't really make sense...  
  
Why? Honestly, it makes total sense [**edit: to me**] and this is the way I would do it off the top of my head. Is there a reason I'm not seeing why this is a suboptimal option?

---

## Comment 6

> Username: m-j-w  
> Created at: 2016-01-04 23:31:42 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-168843983  

I should rephrase: However, for a tag that didn't immediately make sense at the time since tags are as well some sort of type class... And I have to compare them often.  
  
Or, well, I could do something like that, couldn't I ?  
  
``` cpp  
template <typename T> struct my_tag_wrapper_tag {};  
struct my_sub_tag;  
using my_tag = my_tag_wrapper_tag<my_sub_tag>;  
struct my_data_type { using hana_tag = my_tag; };  
```  
  
and then add `equal_impl` with a comparison of the sub tags.  
That should get me around both issues, right ?

---

## Comment 7

> Username: m-j-w  
> Created at: 2016-01-05 13:39:37 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-169001118  

I'll close this issue. The above serves the purpose. Sorry, I should have come up faster with that trivial solution.

---

## Comment 8

> Username: ldionne  
> Created at: 2016-01-05 13:44:09 UTC  
> Url: https://github.com/boostorg/hana/issues/231#issuecomment-169001980  

> That should get me around both issues, right ?  
  
Yes, although that comparison will be implemented as `std::is_same`, which is the same as comparing `hana::type`s. So basically, you'll be reimplementing the comparison for `hana::type`s, but with the added semantics that the types you're comparing are supposed to be tags. I, again, don't see a reason to do so. But if that suits your needs, then I'd say this is the correct way to go.
