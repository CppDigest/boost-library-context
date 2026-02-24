# #411 Prefixed macros with BOOST [Merged]

> Username: Thihup  
> Created at: 2019-12-16 14:42:08 UTC  
> Updated at: 2019-12-17 10:43:38 UTC  
> Merged at: 2019-12-17 10:43:17 UTC  
> Closed at: 2019-12-17 10:43:17 UTC  
> Url: https://github.com/boostorg/gil/pull/411  

### Description  
  
Prefixes some macros with _BOOST_  
  
### References  
  
This pull request closes https://github.com/boostorg/gil/issues/410

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-12-16 16:24:01 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566133946  

@Thihup Thank you for your contribution, awesome!  
  
Please, refrain from promptly reacting to the further @ned14's suggestions for a moment.  
I'm believe there are no changes needed in the auxiliary files, just C++ files, see https://github.com/boostorg/gil/issues/410#issuecomment-566133063

---

## Comment 2

> Username: Thihup  
> Created_at: 2019-12-16 16:31:44 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566137297  

Hi @mloskot!  
  
I'll wait to see if it's really necessary change the auxiliary files.  
  
Sorry for the inconvenience.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-12-16 16:38:32 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566140011  

> Sorry for the inconvenience.  
  
No need, no problem. I just didn't want you to keep doing work unnecessarily.

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-12-16 16:53:07 UTC  
> Updated_at: 2019-12-16 16:53:29 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566146148  

@Thihup I'd suggest to focus this PR on renaming C++ `#define`-ed macros only in all `.hpp` and `.cpp` files. Sounds good, do you agree?  
  
Review of the auxiliary files is a good idea, but I'd prefer to do it in separate PR, as per my https://github.com/boostorg/gil/issues/410#issuecomment-566142056

---

## Comment 5

> Username: Thihup  
> Created_at: 2019-12-16 16:59:25 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566148799  

Sounds good to me

---

## Comment 6

> Username: Thihup  
> Created_at: 2019-12-16 17:06:15 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566151537  

Should I prefix the macros in tests too?

---

## Comment 7

> Username: ned14  
> Created_at: 2019-12-16 17:45:34 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566167037  

For tests it's up to you, but for easy checking that you're good it's probably easiest.

---

## Comment 8

> Username: mloskot  
> Created_at: 2019-12-16 18:20:59 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566180261  

@Thihup   
> Should I prefix the macros in tests too?  
  
Which macros you mean?

---

## Comment 9

> Username: Thihup  
> Created_at: 2019-12-16 19:11:58 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566200080  

_GIL_Tests_ (test/legacy/performance.cpp | recreate_image.cpp)   
_GIL_TEST_CHANNEL_CONVERT_FROM_ (test/core/channel/algorithm_channel_convert.cpp) .  
  
I think there is no others cpp to edit but those. If it's not necessary I think it's ready to merge.

---

## Comment 10

> Username: mloskot  
> Created_at: 2019-12-16 19:59:45 UTC  
> Updated_at: 2019-12-16 20:00:03 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566218625  

@Thihup  
> GIL_Tests (test/legacy/performance.cpp | recreate_image.cpp)  
  
This is not an explicit `#define`-ed macro, but test suite name parameter.  
  
This is part of Legacy Tests which are assumed promised as left untouched as per [test/legacy/README.md](https://github.com/boostorg/gil/blob/8e7bda62f5dfc856a62df1b7c7852f3aead17792/test/legacy/README.md).  
  
However, I'd suggest to rename the two `GIL_Tests` to all lower-case `gil_legacy_tests`.  
  
This should avoid confusing the macro check tooling.  
  
> GIL_TEST_CHANNEL_CONVERT_FROM (test/core/channel/algorithm_channel_convert.cpp) .  
  
Yes, this can be prefixed indeed. Good catch.

---

## Comment 11

> Username: mloskot  
> Created_at: 2019-12-17 10:43:38 UTC  
> Url: https://github.com/boostorg/gil/pull/411#issuecomment-566487060  

@Thihup Thank you very much for your help!

---
