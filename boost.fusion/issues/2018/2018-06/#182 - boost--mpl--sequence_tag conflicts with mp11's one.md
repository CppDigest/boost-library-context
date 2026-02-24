# #182 - boost::mpl::sequence_tag conflicts with mp11's one [Closed]

> Username: redboltz  
> Created at: 2018-06-14 05:09:29 UTC  
> Updated at: 2019-12-14 02:59:21 UTC  
> Closed at: 2019-12-14 02:59:21 UTC  
> Url: https://github.com/boostorg/fusion/issues/182  

It seems that   
https://github.com/boostorg/fusion/blob/develop/include/boost/fusion/adapted/std_tuple/tag_of.hpp#L32  
conflicts with  
https://github.com/boostorg/mp11/blob/develop/include/boost/mp11/mpl.hpp#L148  
  
Running demo:  
https://wandbox.org/permlink/BoAQGjmPMWTW0Uwj

---

## Comment 1

> Username: jonesmz  
> Created at: 2019-08-23 02:08:40 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-524144369  

I'm running into this issue as well.  
  
Any advice from boost::fusion?

---

## Comment 2

> Username: djowel  
> Created at: 2019-08-23 02:37:06 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-524149622  

Not sure how to deal with this, yet. Have anyone asked the mpl11 devs ( pings @pdimov )?  Fusion's usage looks legit. Maybe we need to include the right header instead of a forward declaration.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-08-23 16:38:22 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-524382202  

The forward declarations are correct; the problem is that both libraries try to add MPL support to `std::tuple`.  
  
The only option I see is for me to split `mp11/mpl.hpp` into two parts, `mpl_list.hpp`, which will add MPL support for `mp_list`, and `mpl_tuple.hpp`, which will contain the `std::tuple` support. This will leave to the user the choice of whether to give `std::tuple` to Fusion or Mp11.

---

## Comment 4

> Username: jonesmz  
> Created at: 2019-08-23 17:25:04 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-524396669  

Forgive me for not knowing the internal details of either library, but it is strange to me that boost::fusion is defining something in the boost::mpl namespace at all.  
  
Can boost::fusion be adjusted to use a different way of doing it?

---

## Comment 5

> Username: pdimov  
> Created at: 2019-08-23 17:34:41 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-524399700  

This is a documented MPL customization mechanism. What Fusion is doing is fine. The problem is that nobody really owns `std::tuple`, as that's a standard type, not part of any library, Boost or otherwise.

---

## Comment 6

> Username: djowel  
> Created at: 2019-08-23 23:45:20 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-524494154  

> The only option I see is for me to split `mp11/mpl.hpp` into two parts, `mpl_list.hpp`, which will add MPL support for `mp_list`, and `mpl_tuple.hpp`, which will contain the `std::tuple` support. This will leave to the user the choice of whether to give `std::tuple` to Fusion or Mp11.  
  
Perhaps fusion can also introduce a PP macro that allows one to inhibit the `std::tuple` specializations, as a workaround. or, hmmm, maybe just inhibit it all together when mp11 is detected (probably needs to test that tests are still passing as expected, or find ways to make it work with mpl11 right out of the box).

---

## Comment 7

> Username: pdimov  
> Created at: 2019-12-04 23:49:46 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-561897285  

In https://github.com/boostorg/mp11/commit/164aa375f2463ef3d3bf717471b872ababf947f4, I've split `mpl.hpp` into `mpl_list.hpp` and `mpl_tuple.hpp`, so in case of a conflict with Fusion libraries can now include just `mpl_list.hpp` and leave `std::tuple` to Fusion.

---

## Comment 8

> Username: djowel  
> Created at: 2019-12-05 03:28:35 UTC  
> Url: https://github.com/boostorg/fusion/issues/182#issuecomment-561951511  

> In [boostorg/mp11@164aa37](https://github.com/boostorg/mp11/commit/164aa375f2463ef3d3bf717471b872ababf947f4), I've split `mpl.hpp` into `mpl_list.hpp` and `mpl_tuple.hpp`, so in case of a conflict with Fusion libraries can now include just `mpl_list.hpp` and leave `std::tuple` to Fusion.  
  
Wonderful! Thanks @pdimov
