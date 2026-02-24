# #202 - [pair] Compress pairs where first is Constant. [Closed]

> Username: ricejasonf  
> Created at: 2015-11-12 00:53:38 UTC  
> Updated at: 2015-11-13 20:38:03 UTC  
> Closed at: 2015-11-13 18:48:05 UTC  
> Url: https://github.com/boostorg/hana/issues/202  

Assuming it's within Hana's scope to use maps as run-time storage,  
  
I propose, for a pair whose first value is a compile-time 'Constant', that the container not actually store the first value. With pair as it is now, a 'compressed' empty type gets padded up to 4 bytes which adds unnecessary storage overhead for large maps. Consider the sizes output of the following program:  
  
``` c++  
#include<boost/hana.hpp>  
#include<vector>  
#include<iostream>  
  
namespace hana = boost::hana;  
  
struct Tag1 {};  
struct Tag2 {};  
struct Tag3 {};  
  
using Vector = std::vector<int>;  
  
template<int i>  
auto buildLargeMap() {  
  return hana::transform(  
    hana::unpack(hana::range_c<int, 0, i>, hana::make_tuple),  
    [](auto x) {  
      return hana::make_pair(x, Vector{});  
    });  
}  
  
template<int i>  
auto buildLargeTuple() {  
  return hana::transform(  
    hana::unpack(hana::range_c<int, 0, i>, hana::make_tuple),  
    [](auto x) {  
      return Vector{};  
    });  
}  
  
int main() {  
  constexpr auto t = hana::tuple_t<Vector, Vector, Vector>;  
  
  std::cout << sizeof(hana::type_c<Tag1>) << std::endl; // 1  
  std::cout << sizeof(5) << std::endl; // 4  
  std::cout << sizeof(hana::make_pair(hana::type_c<Tag1>, 5)) << std::endl; // 8  
  std::cout << sizeof(buildLargeTuple<500>()) << std::endl; // 12000  
  std::cout << sizeof(buildLargeMap<500>()) << std::endl; // 16008  
}  
```  
  
Oh and this would require the 'first' be default constructible.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-12 01:16:22 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-155966377  

> Assuming it's within Hana's scope to use maps as run-time storage,  
  
It definitely is.  
  
> I propose, for a pair whose first value is a compile-time 'Constant', that the container not actually store the first value.  
  
Actually, all of Hana's containers _should_ already compress the representation of empty members. In other words, as soon as an element `std::is_empty`, it is EBO'd out. However, you just made me realize that I have a bug that causes `hana::tuple<hana::pair<...>>` and `hana::tuple<hana::type<>>` and related types not to take up 0 storage. This is because all these types share a common base class, `hana::detail::operator::adl`, which is an implementation detail used to provide common operators. I'll fix this.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2015-11-12 02:56:26 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-155982881  

Sounds great. I wonder if that issue would also cause bloat in the target executable.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-11-12 02:57:48 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-155983048  

Yes it would. It's pretty important. I'm working on a fix right now.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-11-12 19:44:48 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156213506  

@ricejasonf This turns out to be more challenging than I thought. I'm working on it, but it might take a little while before I can provide a clean fix. Also, FWIW, please note that you could also write your `buildLargeMap` and `buildLargeTuple` functions as follow:  
  
``` c++  
template <int i>  
auto buildLargeMap() {  
  return hana::unpack(hana::range_c<int, 0, i>, [](auto ...x) {  
    return hana::make_tuple(hana::make_pair(x, Vector{})...);  
  });  
}  
  
template <int i>  
auto buildLargeTuple() {  
  return hana::replicate<hana::tuple_tag>(Vector{}, hana::int_c<i>);  
}  
```  
  
Altough it does not change the fact that we have a bug in the EBO. The above should be slightly more compile-time efficient, but it shouldn't change much for this example.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2015-11-12 20:46:48 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156229013  

sweet

---

## Comment 6

> Username: ldionne  
> Created at: 2015-11-13 17:37:45 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156496837  

Ok, so I have a fix for this particular use case which will make the tuple of vectors and the tuple of pairs have the same size. However, if you create a tuple of pairs containing only empty objects, the resulting tuple will not have 0 size. This is harder to implement, and I'm not sure it's worth it anyway.

---

## Comment 7

> Username: ricejasonf  
> Created at: 2015-11-13 18:32:11 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156511297  

I confirmed with the original example that it works, and with your suggested improvements it actually does improve compile-time by a significant amount. However if I wrap the contents of buildLargeMap in `hana::to<hana::map_tag>()`, it hangs for over 6 minutes and produces an error. I'm rerunning the test to output to a file so I can see what it actually is, but in the meantime here is the compile times between my implementation and after your suggested improvements.  
  
```  
~/Projects/large_map/b5547a9a4a11b46619ed$ vi large_map.cpp   
~/Projects/large_map/b5547a9a4a11b46619ed$ time clang++ -I /usr/local/src/hana/include/ --std=c++14 large_map.cpp   
  
real    0m4.636s  
user    0m4.381s  
sys 0m0.161s  
~/Projects/large_map/b5547a9a4a11b46619ed$ vi large_map.cpp   
~/Projects/large_map/b5547a9a4a11b46619ed$ time clang++ -I /usr/local/src/hana/include/ --std=c++14 large_map.cpp   
  
real    0m2.973s  
user    0m2.827s  
sys 0m0.130s  
~/Projects/large_map/b5547a9a4a11b46619ed$ vi large_map.cpp   
~/Projects/large_map/b5547a9a4a11b46619ed$ time clang++ -I /usr/local/src/hana/include/ --std=c++14 large_map.cpp   
  
```

---

## Comment 8

> Username: ricejasonf  
> Created at: 2015-11-13 18:36:15 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156512520  

Here is the error although perhaps it's not really a bug. idk.  
  
```  
In file included from large_map.cpp:1:  
In file included from /usr/local/src/hana/include/boost/hana.hpp:55:  
In file included from /usr/local/src/hana/include/boost/hana/adjust.hpp:18:  
/usr/local/src/hana/include/boost/hana/equal.hpp:106:45: fatal error: recursive template instantiation exceeded maximum depth of 256  
            constexpr auto eq = hana::equal(hana::value<X>(), hana::value<Y>());  
                                            ^  
/usr/local/src/hana/include/boost/hana/equal.hpp:51:23: note: in instantiation of function template specialization 'boost::hana::equal_impl<boost::hana::integral_constant_tag<int>, boost::hana::integral_constant_tag<int>, boost::hana::when<true> >::apply<boost::hana::integral_constant<int, 229>, boost::hana::integral_constant<int, 228> >' requested here  
        return Equal::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
                      ^  
/usr/local/src/hana/include/boost/hana/functional/partial.hpp:79:20: note: in instantiation of function template specialization 'boost::hana::equal_t::operator()<const boost::hana::integral_constant<int, 229> &, boost::hana::integral_constant<int, 228> &>' requested here  
            return hana::get_impl<0>(storage_)(  
                   ^  
/usr/local/src/hana/include/boost/hana/any_of.hpp:78:39: note: in instantiation of function template specialization 'boost::hana::partial_t<std::__1::integer_sequence<unsigned long, 0>, boost::hana::equal_t, boost::hana::integral_constant<int, 229> >::operator()<boost::hana::integral_constant<int, 228> &>' requested here  
                auto cond = hana::if_(pred(hana::at_c<k>(xs)), hana::true_c,  
                                      ^  
@                                                                                                                                                                                                                                                                  
```

---

## Comment 9

> Username: ldionne  
> Created at: 2015-11-13 18:37:29 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156512975  

> However if I wrap the contents of buildLargeMap in hana::tohana::map_tag(), it hangs for over 6 minutes and produces an error.  
  
There are a few reasons for this. First, the current implementation of `hana::map` is pretty naive, so the performance is quite shitty. Second, you're doing something that's pretty difficult to do efficiently anyway, because `hana::to<hana::map_tag>` allows for duplicate elements. Basically, you're calling `hana::insert` 500 times, starting with an empty map and building it up.  
  
If you _might_ have duplicates in your tuple, you must do this. Otherwise, just use `hana::unpack(tuple_of_pairs, hana::make_map)` and it should be much better.

---

## Comment 10

> Username: ricejasonf  
> Created at: 2015-11-13 18:46:07 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156518062  

ah, makes sense. Yeah I switched make_tuple with make_map, and it worked in like 3 seconds. Thank you.

---

## Comment 11

> Username: ldionne  
> Created at: 2015-11-13 18:47:27 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156518872  

It _is_ a bug that `hana::map` is so bad, but at least there are workarounds for now. By the way, if you want to make `hana::map` better, let me know :-).

---

## Comment 12

> Username: ricejasonf  
> Created at: 2015-11-13 20:38:03 UTC  
> Url: https://github.com/boostorg/hana/issues/202#issuecomment-156550009  

#197
