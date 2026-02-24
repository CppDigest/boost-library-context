# #340 X3: Dereference a single item view instead of unwrapping sequence [Merged]

> Username: Kojoley  
> Created at: 2017-12-22 15:44:07 UTC  
> Updated at: 2019-01-19 23:53:45 UTC  
> Merged at: 2017-12-29 23:50:04 UTC  
> Closed at: 2017-12-29 23:50:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/340  

Most of discussion happened in https://github.com/boostorg/spirit/pull/219#issuecomment-352905393.  
Brief overall: Currently we do not know what a8e391b was fixing (and it seems partly redundant), but it introduced an unwanted behavior of passing `fusion::iterator_range` to attribute-generating parsers.  
  
This PR reimplements a spirit of a8e391b commit. It tries to pass through as many attributes as  
possible. For example: a single item sequences like `fusion::vector<T>` will be passed through,  
while fusion iterators, fusion iterator range or view of size one will be dereferenced before passing  
to underlying parsers of sequence parser.  
  
TODO:  
  - [x] Tests? It is not that easy to test, but I can make a custom parser and check attribute type in it.  
  - [x] Should `tuple_traits.hpp` be renamed? The name already does not reflect the content.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-22 23:56:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-353692347  

- Looks good to me  
- I like the idea of a custom parser test  
- Any suggestion on renaming tuple_traits.hpp? It still looks good to me.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-23 00:10:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-353693571  

> Any suggestion on renaming tuple_traits.hpp? It still looks good to me.  
  
It does not contain a single word 'tuple' except in a include guard. It should have been named `sequence_traits`, but now, with `is_size_one_view` added, I do not know.

---

## Comment 3

> Username: djowel  
> Created_at: 2017-12-23 00:23:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-353694584  

> Any suggestion on renaming tuple_traits.hpp? It still looks good to me.  
>   
> It does not contain a single word 'tuple' except in a include guard. It should have been named sequence_traits, but now, with is_size_one_view added, I do not know.  
  
Ah, it's the name "tuple". It's just that it is an attribute category name in X3 for "tuple" like types (tuple_attribute). Let's keep it as it is.

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-12-27 16:05:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354135049  

I am too tired of complexity. It is already looks pretty scary. I wish C++ has Python generators.

---

## Comment 5

> Username: djowel  
> Created_at: 2017-12-28 00:07:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354198873  

> I am too tired of complexity. It is already looks pretty scary. I wish C++ has Python generators.  
  
Yeah, I wish the code can be simplified. I too am tired of such complexity. It's hard to reason out what the semantics of the code is by looking at the code. Perhaps there's a way to conceptualize the semantics with some smart meta-constructs.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-12-28 00:14:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354200028  

It can be simplified by removing the test :D  
Currently it does not check a lot of cases, adding generator for permutations will require more code. I do not know how to simplify this except placing hundred hardcoded types.

---

## Comment 7

> Username: djowel  
> Created_at: 2017-12-28 05:24:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354231026  

Let's just have this as it is now. Is there any other reason that's keeping you from pushing the code?

---

## Comment 8

> Username: Kojoley  
> Created_at: 2017-12-28 21:00:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354357081  

> Is there any other reason that's keeping you from pushing the code?  
  
The solution looks solid for me. The test catches both problems before and after a8e391b.  
So, I think it is ready for merge.

---

## Comment 9

> Username: djowel  
> Created_at: 2017-12-28 21:13:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354358832  

👍 Feel free to merge, @Kojoley

---

## Comment 10

> Username: Kojoley  
> Created_at: 2017-12-28 21:20:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354359718  

I am afraid of adding anything to `namespace std`. Because of `BOOST_TEST_EQ` I was needed to add a stream operator for `std::pair`. How do you think, may be just replace it with `BOOST_TEST(a == b)`?

---

## Comment 11

> Username: djowel  
> Created_at: 2017-12-28 21:28:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354360503  

Fair point. Yeah, it's best to avoid adding to namespace std (best practice, etc.). There should be a couple of ways, BOOST_TEST(a == b) being one of them.

---

## Comment 12

> Username: Kojoley  
> Created_at: 2017-12-29 17:46:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354477712  

Huh, made the test twice shorter.

---

## Comment 13

> Username: djowel  
> Created_at: 2017-12-29 19:29:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/340#issuecomment-354490250  

OK, LGTM 👍

---
