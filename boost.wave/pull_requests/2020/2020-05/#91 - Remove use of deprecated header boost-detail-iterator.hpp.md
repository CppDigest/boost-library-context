# #91 Remove use of deprecated header boost/detail/iterator.hpp [Merged]

> Username: Lastique  
> Created at: 2020-05-11 18:51:36 UTC  
> Updated at: 2020-05-12 15:48:02 UTC  
> Merged at: 2020-05-12 15:28:52 UTC  
> Closed at: 2020-05-12 15:28:52 UTC  
> Url: https://github.com/boostorg/wave/pull/91  

The header is deprecated in favor of `<iterator>`. It generates compiler warnings and will be removed in a future release.  
  
Also, added a missing include for `iterator_value` trait.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2020-05-11 20:27:46 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626942352  

Thanks for the PR @Lastique . Will this code work OK with C++03?

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-05-11 20:30:51 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626944243  

Yes, I see no reason why it shouldn't.

---

## Comment 3

> Username: jefftrull  
> Created_at: 2020-05-11 20:41:32 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626950676  

Thank you. Do we need `<iterator>` at all? I don't see us using any iterator traits from `std`. Should we remove the Boost traits entirely and use `std::iterator_traits<Iterator>::value_type` instead?

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-05-11 20:44:06 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626952032  

There is a call to `std::advance` down in the code.

---

## Comment 5

> Username: jefftrull  
> Created_at: 2020-05-11 20:48:10 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626954262  

Ah, OK. What do you think about replacing `boost::iterators::iterator_value` with `std::iterator_traits<>::value_type` and eliminating the Boost header entirely?

---

## Comment 6

> Username: Lastique  
> Created_at: 2020-05-11 20:54:17 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626957520  

Sure, I can do that.

---

## Comment 7

> Username: Lastique  
> Created_at: 2020-05-11 20:58:19 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-626959680  

Done.

---

## Review 8 [Approved]

> Username: hkaiser  
> Created_at: 2020-05-12 00:24:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/91#pullrequestreview-409618976  

LGTM, thanks!

---

## Comment 9

> Username: jefftrull  
> Created_at: 2020-05-12 03:05:58 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-627080352  

@Lastique Travis failed with a Clang issue related to Filesystem - it looks like you were the last to touch the associated file :) Would you be willing to look at [the failure](https://travis-ci.org/github/boostorg/wave/jobs/685844572)?

---

## Comment 10

> Username: Lastique  
> Created_at: 2020-05-12 08:14:48 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-627187386  

That failure is unrelated to this PR. Looks like `struct stat` has unexpected layout on Darwin, which makes Boost.Filesystem fail to compile. I'll take care of that in Boost.Filesystem.

---

## Comment 11

> Username: Lastique  
> Created_at: 2020-05-12 12:25:54 UTC  
> Url: https://github.com/boostorg/wave/pull/91#issuecomment-627310420  

You can restart the CI job, it should succeed now.

---
