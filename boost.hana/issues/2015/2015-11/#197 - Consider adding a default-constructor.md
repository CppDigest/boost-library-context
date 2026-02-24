# #197 - [map] Consider adding a default-constructor [Closed]

> Username: ldionne  
> Created at: 2015-11-02 23:18:58 UTC  
> Updated at: 2016-02-01 15:13:13 UTC  
> Closed at: 2016-02-01 15:13:13 UTC  
> Url: https://github.com/boostorg/hana/issues/197  

Requested in [the chat](https://gitter.im/boostorg/hana?at=5637ee313003682f1408dbc0).

---

## Comment 1

> Username: ricejasonf  
> Created at: 2015-11-03 01:41:59 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-153213772  

For a map whose keys are types and values are default constructible containers, it would be cool if the containing map itself was default constructible. In my case I am creating a reusable type from an external function that takes parameters that I would have to capture otherwise. My current workaround is to pass around a tuple of pairs type and convert it to a map in the constructor which is less convenient. Please consider this simplified use case:  
  
```  
#include<boost/hana.hpp>  
#include<vector>  
  
namespace hana = boost::hana;  
  
struct Fish {};  
struct Cat {};  
struct Dog {};  
  
struct Tag1 {};  
struct Tag2 {};  
struct Tag3 {};  
  
template<typename T>  
struct Store  
{  
  std::vector<T> values;  
};  
  
auto storeMap()  
{  
  return hana::zip_with(hana::make_pair,  
      hana::tuple_t<Tag1, Tag2, Tag3>,  
      hana::make_tuple(  
        Store<Fish>{},  
        Store<Cat>{},  
        Store<Dog>{}  
      ));  
}  
  
using StoreMap = decltype(storeMap());  
  
template<typename Map_>  
class Context  
{  
  using ActualMap = decltype(hana::unpack(Map_{}, hana::make_map));  
  ActualMap stores;  
  
  public:  
  
  Context()  
    : stores(hana::unpack(Map_{}, hana::make_map))  
  { }  
  
  template<typename T>  
  auto& get(T)  
  {  
    return stores[hana::type_c<T>];  
  }  
};  
  
int main() {  
  Context<StoreMap> ctx1;  
  Context<StoreMap> ctx2;  
  
  Store<Dog>& dog_store1 = ctx1.get(Tag3{});  
  Store<Dog>& dog_store2 = ctx2.get(Tag3{});  
}  
```  
  
Let me know if this is just crazy or if I am abusing decltype here. I was originally doing something like this with C++11 std::tuples, and it was not pretty :).

---

## Comment 2

> Username: ricejasonf  
> Created at: 2015-11-04 18:28:55 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-153821260  

Incidentally I realized I can also use `hana::to<hana::map_tag>(tuple_of_pairs)`, but for my specific case I just made a wrapper to call storeMap() on construction.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2015-11-13 20:32:34 UTC  
> Updated at: 2015-11-13 20:35:42 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-156548870  

I made three tests using variations of a `buildLargeMap` example function template. It appears that there is considerable bloat when I don't use the `decltype` hole in the sheet to instantiate a `hana::map`.  
- Test 1: I just create an instance using the function directly.  
- Test 2: I `decltype` a tuple of pairs and then use unpack to instantiate the map.  
- Test 3: I add a naive default constructor to map and then `decltype` the map  
  
Here is the resulting target sizes compiled with `-O3` (note that using `strip` doesn't change much):  
  
```  
-rwxr-xr-x   1 jasonrice  staff  222024 Nov 13 12:10 large_map_1  
-rwxr-xr-x   1 jasonrice  staff  201440 Nov 13 12:10 large_map_2  
-rwxr-xr-x   1 jasonrice  staff   52120 Nov 13 12:10 large_map_3  
```  
  
I'm trying to understand what is going on here. The third example is pretty extreme so I'll do more to check that the object creation isn't completely obviated. Here is the source code:  
  
```  
//test 1  
#include<boost/hana/map.hpp>  
#include<boost/hana/range.hpp>  
#include<vector>  
  
namespace hana = boost::hana;  
  
using Vector = std::vector<int>;  
  
template <int i>  
auto buildLargeMap() {  
  return hana::unpack(hana::range_c<int, 0, i>, [](auto ...x) {  
    return hana::make_map(hana::make_pair(x, Vector{})...);  
  });  
}  
  
int main() {  
  auto x = buildLargeMap<500>();  
}  
  
//test 2  
#include<boost/hana/map.hpp>  
#include<boost/hana/range.hpp>  
#include<vector>  
  
namespace hana = boost::hana;  
  
using Vector = std::vector<int>;  
  
template <int i>  
auto buildLargeMap() {  
  return hana::unpack(hana::range_c<int, 0, i>, [](auto ...x) {  
    return hana::make_tuple(hana::make_pair(x, Vector{})...);  
  });  
}  
  
using LargeMap = decltype(buildLargeMap<500>());  
  
int main() {  
  auto x = hana::unpack(LargeMap{}, hana::make_map);    
}  
  
//test 3  
#include "./map.hpp"  
#include<boost/hana/range.hpp>  
#include<vector>  
  
namespace hana = boost::hana;  
  
using Vector = std::vector<int>;  
  
template <int i>  
auto buildLargeMap() {  
  return hana::unpack(hana::range_c<int, 0, i>, [](auto ...x) {  
    return hana::make_map(hana::make_pair(x, Vector{})...);  
  });  
}  
  
using LargeMap = decltype(buildLargeMap<500>());  
  
int main() {  
  auto x = LargeMap{};  
}  
  
```  
  
I have a cmake file and the modified map.hpp, if you want the whole thing in a Gist or something. lmk

---

## Comment 4

> Username: ldionne  
> Created at: 2015-11-13 22:11:45 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-156573236  

First of all, `tuple` versus `map` shouldn't change much. In the current state of things, a `map` is really nothing but a `tuple` with associative semantics. So it is sufficient to test for `tuple`s of `pair`s.  
  
That being said, I would argue that there's both a compiler QOI issue behind this, and a user abuse. In other words, no matter how clever the compiler and Hana are, you're still placing 500 `std::vector`s on the stack and moving them around. It might be possible to mitigate the issue, however, and I'm still experimenting with it.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2015-11-13 23:52:37 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-156592784  

Yeah, if I change it to just `int`, I actually get `unused-variable` warnings and only the second example weighs in at around 60k where the others both 4,248 bytes which I think is just a shell of a program.  
  
Sorry if I am being a pest. I think I'm going to make a separate project just for testing this kind of stuff. Would you mind if I asked something like this as a question on StackOverflow.com?

---

## Comment 6

> Username: ldionne  
> Created at: 2015-11-13 23:56:00 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-156593171  

> Yeah, if I change it to just int, I actually get unused-variable warnings and only the second example weighs in at around 60k where the others both 4,248 bytes which I think is just a shell of a program.  
  
I made some experiments, and basically the same thing happens if you have a struct holding 500 pairs. If you hold 500 vectors, the code is still shitty but it's better. However, when you have a `std::array` of 500 vectors, the compiler seems to understand that it must use a loop to initialize the elements, instead of creating a ton of bloat. Hence, I'd call bullshit on the compiler or the code generation, cause from a low-level POV, a struct holding 500 vectors should probably be the same as an array of 500 vectors.  
  
> Would you mind if I asked something like this as a question on StackOverflow.com?  
  
Sure, go ahead.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-11-29 17:05:46 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-160432316  

@ricejasonf Hey, I think I found what was the problem. The bloat is probably due to the symbol of the constructor and destructors of the `hana::tuple`/`hana::map` being embedded in the resulting executable. Since the symbols are external, `strip` won't remove them. However, I tried locally and compiling with `-flto` **and then** using `strip` will do the job.

---

## Comment 8

> Username: ricejasonf  
> Created at: 2015-11-30 19:01:34 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-160725699  

I gave it a shot with `-flto`. The sizes were smaller but still wildly different. Take this with grain of salt though, I realized the Apple version of clang/libc++ is some kind of mystery meat containing bugs that were fixed in clang 3.4.   
  
Back to the original issue, the only reason I see for a map default constructor is convenience, and the only place I am using it is in a wrapper class. A wrapper class that since refactoring to use Hana, I can see that I could eliminate it in favor of using the map directly and probably save myself some bloat in the process.  
  
That said, I'd be fine if you wanted to deem it unnecessary for now and close this.

---

## Comment 9

> Username: ldionne  
> Created at: 2015-11-30 19:05:16 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-160726605  

Can you please put the whole thing in a Gist? I'd like to try it on my side. It's a rather annoying issue, and I think I've got a library-based fix if `-flto` doesn't do the trick.

---

## Comment 10

> Username: ricejasonf  
> Created at: 2015-11-30 19:34:53 UTC  
> Url: https://github.com/boostorg/hana/issues/197#issuecomment-160735136  

https://gist.github.com/ricejasonf/c2cd831d207912583549  
Note that, if you use the CMakeLists.txt, you'll have to add `-flto`.
