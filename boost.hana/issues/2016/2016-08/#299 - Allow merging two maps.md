# #299 - [map] Allow merging two maps [Closed]

> Username: ldionne  
> Created at: 2016-08-25 01:04:55 UTC  
> Updated at: 2017-04-23 14:42:45 UTC  
> Closed at: 2017-04-22 23:55:44 UTC  
> Url: https://github.com/boostorg/hana/issues/299  

Hana does not provide a way to merge two maps out of the box, although that would be very useful. For now, one has to use `fold_left` with `insert` to insert each element of one map into the other:  
  
``` c++  
#include <boost/hana.hpp>  
#include <string>  
namespace hana = boost::hana;  
using namespace hana::literals;  
  
constexpr auto m1 = hana::make_map(  
    hana::make_pair("key1"_s, hana::type_c<std::string>),  
    hana::make_pair("key2"_s, hana::type_c<std::string>)  
);  
  
constexpr auto m2 = hana::make_map(  
    hana::make_pair("key3"_s, hana::type_c<std::string>),  
    hana::make_pair("key4"_s, hana::type_c<std::string>),  
    hana::make_pair("key5"_s, hana::type_c<std::string>)  
);  
  
constexpr auto result = hana::fold_left(m1, m2, hana::insert);  
constexpr auto expected = hana::make_map(  
    hana::make_pair("key1"_s, hana::type_c<std::string>),  
    hana::make_pair("key2"_s, hana::type_c<std::string>),  
    hana::make_pair("key3"_s, hana::type_c<std::string>),  
    hana::make_pair("key4"_s, hana::type_c<std::string>),  
    hana::make_pair("key5"_s, hana::type_c<std::string>)  
);  
  
static_assert(result == expected, "");  
  
int main() { }  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-08-25 03:33:15 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-242272528  

I would expect that if both `m1` and `m2` had "key1" set to different values, the value in `m2` would prevail which is not the case with `hana::insert`.

---

## Comment 2

> Username: shreyans800755  
> Created at: 2017-04-02 05:58:39 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-290966752  

@ricejasonf @ldionne   
I would like to work on this feature. What kind of interface are we looking for ?  
Are we looking for something like, hana::merge_map(m1, m2) or operator like + and += on two maps ?

---

## Comment 3

> Username: ricejasonf  
> Created at: 2017-04-02 07:56:34 UTC  
> Updated at: 2017-04-02 07:56:53 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-290971086  

`hana::map` could possibly be implemented as a `hana::Monoid`. I don't think `+=` would make sense as it suggests the first map is being modified.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-04-02 17:21:41 UTC  
> Updated at: 2017-04-02 17:21:53 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-291000421  

@shreyans800755 Thanks for your interest! I think we could reuse `hana::union_`, since a "merge" is really a union of two maps. So it would be a matter of  
  
1. Specialize `hana::union_` for `map_tag`  
2. Document that `hana::union_` works for maps too  
3. Add tests for `hana::union_` on maps  
  
Does that make sense?

---

## Comment 5

> Username: shreyans800755  
> Created at: 2017-04-02 18:50:38 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-291006399  

@ldionne it perfectly makes sense. I'll try to complete it asap.

---

## Comment 6

> Username: ldionne  
> Created at: 2017-04-02 19:17:17 UTC  
> Updated at: 2017-04-02 19:17:24 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-291008109  

Take it easy! Feel free to submit an incomplete PR to gather early feedback if you want, too.

---

## Comment 7

> Username: ldionne  
> Created at: 2017-04-22 23:55:44 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-296409344  

Closed by #338. Thanks @shreyans800755! Note that we should also probably add a `hana::map` version of other set-theoretic operations.

---

## Comment 8

> Username: shreyans800755  
> Created at: 2017-04-23 14:42:45 UTC  
> Url: https://github.com/boostorg/hana/issues/299#issuecomment-296448066  

@ldionne I can work on that. May be I'll create an issue first and we can discuss it over gitter.
