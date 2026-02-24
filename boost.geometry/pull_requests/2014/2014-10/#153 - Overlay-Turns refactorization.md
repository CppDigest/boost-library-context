# #153 Overlay/Turns refactorization [Merged]

> Username: awulkiew  
> Created at: 2014-10-08 18:45:07 UTC  
> Updated at: 2014-10-31 13:39:49 UTC  
> Merged at: 2014-10-10 18:32:55 UTC  
> Closed at: 2014-10-10 18:32:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/153  

There are 2 goals for now:  
1. To remove redundant `turn_operation<>::other_id` _(the first commit)_.  
2. To replace `int` indexes in `segment_identifier` and various algorithms to `signed_index_type` equal to `std::ptrdiff_t` or `boost::make_signed<std::size_t>::type` _(in progress)_.  
  
ad1. There are 2 places left where `other_id` can be found.  
- `algorithms/detail/overlay/handle_tangencies.hpp`: in the commented debug code, I thought that it might be useful for someone, but I may remove it.  
- `algorithms/detail/overlay/stream_info.hpp`: this looks like some debug tool for an old version of `turn_info` (when it contained data now in the `result_type` of `IntersectionPolicy`?), so AFAIU it shouldn't work. I wasn't sure if we should fix it or remove it.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-10-08 21:37:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58432018  

Thanks Adam. I glanced through it, it looks good, and I'm OK with the merge - assuming all unit tests pass of course. Many things are in Menelaos parts so he should look at it too.  
  
The parts in debug code can be removed too. About stream_info, I don't know when I last used it, if it is really out of date, just remove it.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-10-08 22:50:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58440642  

Ok I replaced `int`s for segments indexes in places I spotted besides in `buffer`.  
  
In `buffer` there are a lot of `int` indexes, turns and parts are indexed using `int`s, e.g. in `buffered_piece_collection.hpp`. Plus some implicit conversions from `size_t` to `int`. I didn't knew if I should and might touch them.  
  
Turns are also indexed using `int` in other parts of the library, e.g. in _overlay.hpp_. In many places they're initialized with `-1` so they should be signed types which is ok (Boost,Range uses signed type internally in `size()` anyway) but `Turns` indexes should be of type `range_difference<Turns>::type`, `range_size<Turns>::type` or even better `Turns::size_type`, same with parts and any other Container/Range. In many places it could be applied in-place, otherwise we might also use `signed_index_type` which would be safer than `int`.  
  
So my question is, may I go further with refactoring and replace those indexes with types more "appropriate" than `int`?  
Should I leave the `buffer` untouched?  
  
SIDE NOTES:  
  
There are also places where instead of using `range_size<>::type` or `Container::size_type` we're using `std::size_t` but I guess this is less dangerous.  
  
Btw, unsigned types may also be initialized with -1 and as long as those values are only indicators of an "invalid" state used only in checks everything should be ok. But I guess it'd be safer at this stage to use signed types in such cases because some parts are written with having signed types in mind, e.g. comparisons are done with `< 0` or `abs()` is used, the worst is of course simple subtraction. But this doesn't mean that this couldn't be done. However it's probably not necessary and would result in some errors.  
  
But there are places where it would be safe to use unsigned types, e.g. in Menelaos' graph for parent_id which AFAIU is signed only because it must be initialized with some "invalid" value and is not used in any calculus. Initializing it with -1 would be the same as the initialization with `std::numeric_limits<size_type>::max()`, like `std::string::npos`. Btw, I replaced `parent_id`'s type with `signed_index_type` to be more compatible with `std::size_t`, I hope it's ok.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-10-09 09:43:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58485846  

I noticed that Turns (and other internal containers) sometimes are handled as Ranges, sometimes as Containers. E.g. in `traverse::apply()` types are defined as `boost::range_iterator<Turns>::type` (Range concept ) but the size is retrieved using `turns.size()` (Container concept). Personally I think that treating it as a Container is closer to the truth. `size_type` is defined by the (std::)allocator. Using Boost.Range we can only have a type defined in `boost::make_unsigned<>` for `difference_type` and I'm not sure if this is ok for some unusual platforms. Of course Range concept is more flexible and in fact is used e.g. in interrupt policies where a `std::pair<>` is passed, but it's then nicely called as `Range`. Do you think that it make sense to use Container concept for containers (e.g. `Container::size_type`, `turns.size()`, `operator[]`, etc.)? Or should all containers passed by parameters, so living somewhere outside, be handled as Ranges?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-10-09 10:37:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58491237  

It's even more important for Turns than for Geometries since in the worst case the number of Turns can be N^2 (consider two zig-zag linestrings intersecting each other). So using the example from the discussion about the commit https://github.com/boostorg/geometry/commit/df40ef3bc7470c62bc9f1af27075482ba259b24f, to generate 2G (a limit for 32bit int) turns in the worst case we need 2 Linestrings containing 46341 Points each, that's quite small number. However Turns are a lot bigger than Points from the example, they contain the Point itself and a lot of additional data, they take 112B on MSVC (so huge!) instead of 8B for Point so one would need more than 224GB of RAM for such container, and this is less probable, still possible, just not in most people homes (supercomputers has TB of RAM).  
  
Btw, a Turn takes 112B! Cache isn't very happy with this. We should definietly think about optimizing it, start from:  
- compressing all flags and enums on e.g. 4B  
- storing only required indexes in segment_index  
- throw away some unneeded members, though probably all are required  
- etc.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-10-09 20:37:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58573532  

I went through the changes. Everything that was changed in code that I wrote seems perfectly okay. So I am fine with the changes and with merging them.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2014-10-09 21:04:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58577429  

Yes we could look at the turns, they indeed can be compressed a bit. I did not look in detail yet, but I think they will always be a lot larger than a point. The segment_ratio's also increased their sizes. You don't gain much by compressing them by 10 or 20%. Compressing flags decreases storage but can increase accesstime, so performance.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-10-09 21:32:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58581219  

@mkaravel Great, thanks for reviewing.  
  
@barendgehrels It's a strong probability that loading Turns into the cache takes more time than processing data. Check out my test https://github.com/awulkiew/test-tuple_vector and resources mentioned there. Turn is really huge, it's 112B! In the test I stored more or less 40B objects and loading the next chunk of data from memory took longer than calculating 2x sqrt because of cache misses (hot data is stored along with the cold data). This is another possibility, divide the Turn into hot and cold parts. Data layout is very important these days. In particular, it's a strong probability that loading the compressed flags into the cache would take more time than performing some binary operations on them, and this change would be a win-win, less size and more speed. Of course we could do some benchmarking first.  
  
I propose to review this chunk of changes and merge it first.  
Then I propose to replace various indexes and sizes types with something more appropriate than `int`.  
And then we could think about something else.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2014-10-10 17:53:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/153#issuecomment-58691898  

OK, sure, I'm OK that this is investigated.

---
