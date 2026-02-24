# #170 Cleaned up container_traits. [Merged]

> Username: octopus-prime  
> Created at: 2016-01-26 20:55:46 UTC  
> Updated at: 2016-02-21 19:43:31 UTC  
> Merged at: 2016-02-21 19:43:31 UTC  
> Closed at: 2016-02-21 19:43:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/170  

Cleaned up push_back_container and append_container.  
  
Removed reserve optimization in append_container since we must provide reserve for different container types (e.g. string and deque) and the container implementation should decide how to append elements as fast as possible.

---

## Comment 1

> Username: djowel  
> Created_at: 2016-01-26 20:58:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175226735  

Oops. We need that optimization... please to find another way.

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2016-01-26 21:06:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175230420  

Hm... How to provide reserve for so many container types?  
  
string, vector, deque, unordered_map, unordered_multimap, unordered_set, unordered_multiset...

---

## Comment 3

> Username: octopus-prime  
> Created_at: 2016-01-26 21:32:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175240802  

Reserve optimization for most important types.

---

## Comment 4

> Username: djowel  
> Created_at: 2016-01-26 21:35:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175241744  

yes, we'll have to do some special cases here. that's necessary for optimization.

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2016-01-27 17:52:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175767918  

Do you have some simple grammars for testing container traits using append?

---

## Comment 6

> Username: hkaiser  
> Created_at: 2016-01-27 19:47:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175820172  

@octopus-prime What's your rationale for this change? Shouldn't we rather provide specializations for those container types you're mentioning?

---

## Comment 7

> Username: octopus-prime  
> Created_at: 2016-01-27 20:05:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175828015  

@hkaiser Look at pull request #169. There we removed specializations from push_back_container and used the insert() method witch all common containers provide. So we have less code and we provide more container types - out of the box.  
  
The basic idea in pull request #170 was the same for append_container. To remove specializations and use the insert() method witch all common containers provide. You know providing specializations for each container type on earth is endless work with a lot of spaghetti specialization code...  
  
But djowel told me that we need the reserve() optimization. So the the basic idea is gone...  
We need specializations. Perhaps we can close this pull request.  
  
Nevertheless, we should have tests for the append_container. So i need simple grammars for testing container traits using append.

---

## Comment 8

> Username: djowel  
> Created_at: 2016-01-27 21:51:34 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175875477  

The reserve optimization _IS_ important. The tricky part is how to generalize this for all containers. For example, we can use a metaprogram that detects the availability of the reserve member function for a container C.

---

## Comment 9

> Username: octopus-prime  
> Created_at: 2016-01-27 22:51:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175901045  

Done. Please do an intense code review!!

---

## Comment 10

> Username: djowel  
> Created_at: 2016-01-27 22:56:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-175902202  

Where's the "like" button? Boost folks are smart! :-)

---

## Comment 11

> Username: tomilov  
> Created_at: 2016-01-28 04:28:19 UTC  
> Updated_at: 2016-02-21 16:26:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#discussion_r51078745  

`c.end()` return `Container::iterator`, `c.insert` accept `Container::const_iterator`. There is unneeded conversion. Maybe is it better to use `c.cend()` instead? Or even `std::cend(c)`?

---

## Comment 12

> Username: tomilov  
> Created_at: 2016-01-28 04:33:10 UTC  
> Updated_at: 2016-02-21 16:26:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#discussion_r51078936  

unsigned + signed. Is there warning issue for containers with RandomAccessIterators? If so `static_cast< std::size_t >(std::distance(first, last))` would be better.

---

## Comment 13

> Username: tomilov  
> Created_at: 2016-01-28 04:40:16 UTC  
> Updated_at: 2016-02-21 16:26:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#discussion_r51079261  

It better to use `typename detail::has_reserve_method<Container>::type{}` then `std::integral_constant<bool, detail::has_reserve_method<Container>::value>()`.

---

## Comment 14

> Username: octopus-prime  
> Created_at: 2016-01-28 07:42:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-176036311  

@tomilov Thanks!  
- c.cend() ... done.  
- static_cast< std::size_t >(std::distance(first, last)) ... i am not sure.  
- typename detail::has_reserve_method<Container>::type{} ... looks very nice! done.

---

## Comment 15

> Username: tomilov  
> Created_at: 2016-01-29 18:29:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-176899058  

Defenitely there is an issue with multipass guarantee absence for input iterators. For them you cannot call distance, then read the same range once again. One can get logical (or even hard) error when using std::vector+std::istream_iterator combination.

---

## Comment 16

> Username: octopus-prime  
> Created_at: 2016-01-29 18:50:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-176908737  

From Qi documentation:  
  
Backtracking in Spirit.Qi requires the use of the following types of iterator: forward, bidirectional, or random access. Because of backtracking, input iterators cannot be used. Therefore, the standard library classes std::istreambuf_iterator and std::istream_iterator, that fall under the category of input iterators, cannot be used.  
  
Lack of X3 documentation here, but I think the same for X3.

---

## Comment 17

> Username: octopus-prime  
> Created_at: 2016-01-29 19:05:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-176915386  

If you try to use std::istream_iterator<char> as iterator type in x3::parse() you get:  
  
error: conversion from ‘std::input_iterator_tag’ to non-scalar type ‘std::forward_iterator_tag’ requested

---

## Comment 18

> Username: octopus-prime  
> Created_at: 2016-02-14 12:54:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-183883524  

Some progress in merging this pull request?

---

## Comment 19

> Username: djowel  
> Created_at: 2016-02-16 00:16:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-184449949  

allow me to do another review this weekend.

---

## Comment 20

> Username: octopus-prime  
> Created_at: 2016-02-21 10:34:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-186792406  

In container_traits.hpp we have  
  
template <typename Container, typename T>  
inline bool push_back(Container& c, T&& val)  
  
and  
  
template <typename T>  
inline bool push_back(unused_type, T const&)  
  
I think there should be symmetry. So we should change the latter to  
  
template <typename T>  
inline bool push_back(unused_type, T&&)  
  
?  
  
Currently we can't do  
  
x3::traits::push_back(attr, std::make_pair(key, value));  
  
if attr is an unused_type.

---

## Comment 21

> Username: djowel  
> Created_at: 2016-02-21 13:41:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-186824775  

Unless there is an objection (Hartmut?), I think this is good to go.

---

## Comment 22

> Username: octopus-prime  
> Created_at: 2016-02-21 16:30:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-186854489  

Using end() because I got compiler errors with gcc 5.3 on Ubuntu 14.04.  
The include files there want still iterator and not const_iterator.  
end() should discard any of these problems.

---

## Comment 23

> Username: hkaiser  
> Created_at: 2016-02-21 16:33:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-186855150  

LGTM!

---

## Comment 24

> Username: octopus-prime  
> Created_at: 2016-02-21 16:38:18 UTC  
> Updated_at: 2016-02-21 16:44:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/170#issuecomment-186855409  

gcc 5.2.1 on Ubuntu 15.10 can build.  
gcc 5.3.0 on Ubuntu 14.04 can build.  
clang 3.6.2 on Ubuntu 14.04 can build.

---
