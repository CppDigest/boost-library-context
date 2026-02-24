# #232 - Try variant2 as Boost.Variant replacement for C++11 [Closed]

> Username: mloskot  
> Created at: 2019-02-05 14:26:01 UTC  
> Updated at: 2020-04-17 17:26:16 UTC  
> Closed at: 2020-04-17 17:25:20 UTC  
> Url: https://github.com/boostorg/gil/issues/232  

@pdimov's https://github.com/pdimov/variant2 is C++11/14/17 implementation of `std::variant`.  
  
@vinniefalco  suggested:  
> You can just temporarily replace Boost.Variant's variant.hpp with Peter's version (its a single file)  
  
So, if trivial indeed, it may be 1) good to provide alternative to Boost.Variant introduced in #231 by @sdebionne   2) observe anycompilation time speed-up

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-05 14:39:04 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-460661018  

Does `variant` appear in any GIL public interfaces? If not, or if most of GIL's variant usage is private, you can simply introduce the variant2 as a private implementation by storing a copy of the header file in your project, I have done something similar for beast (my variant is even further stripped-down):  
https://github.com/boostorg/beast/blob/18a5f436391ee16a8c615a293734b0b92f50f3c1/include/boost/beast/core/detail/variant.hpp#L21

---

## Comment 2

> Username: mloskot  
> Created at: 2019-02-05 14:49:14 UTC  
> Updated at: 2019-02-05 14:49:40 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-460664877  

> Does `variant` appear in any GIL public interfaces?  
  
It's part of GIL extension, the dynamic_image.  
  
>  you can simply introduce the variant2 as a private implementation  
  
This is an interesting idea, thanks.  
  
/cc @stefanseefeld

---

## Comment 3

> Username: sdebionne  
> Created at: 2020-03-20 07:56:15 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-601574483  

Is there anyone working on this? I am facing ICE issues with the current implementation, and also would like to be able to write lambda visitors, two motivations to get this job done.  
AFAIR, it was not to difficult to move from GIL.Variant to Boost.Variant. In the meantime GIL dropped support for std < c++11 and Boost.Variant2 was released.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-20 09:29:25 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-601606446  

> Is there anyone working on this?   
  
I don't know anyone working on this, it still needs help.   
I was going to look into that soonish, but I'm busy with other maintenance in GIL.  
  
> In the meantime GIL dropped support for std < c++11 and Boost.Variant2 was released.  
  
Yes, indeed, the two blockers have been removed and it is good time to switch the variant.  
  
If you'd like to give it a shot, please have my warm welcome.

---

## Comment 5

> Username: sdebionne  
> Created at: 2020-04-05 16:26:56 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-609443253  

I wonder if we should change the API to use variadic template (and remove MPL sequence), e.g. from  
  
```c++  
// Images is an MPL sequence  
template <typename Images>  
class any_image { ... }  
```  
  
to  
  
```c++  
template <typename ... Images>  
class any_image<Image1> { ... }  
```  
  
would that be an acceptable but breaking change?

---

## Comment 6

> Username: simmplecoder  
> Created at: 2020-04-05 16:56:52 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-609447488  

@sdebionne I believe we could attempt SFINAE to preserve old behavior. From the first sight it looks possible, but there may be complications I am unaware of at the moment.

---

## Comment 7

> Username: pdimov  
> Created at: 2020-04-05 17:00:04 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-609447959  

Assuming that GIL will stop using MPL at some point, old code will break anyway because MPL sequences can't be supported without MPL. So using this as an opportunity to switch to `any_image<Image...>` seems sensible.

---

## Comment 8

> Username: mloskot  
> Created at: 2020-04-05 17:02:39 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-609448289  

Yes, dropping use/support for MPL has been considered a breaking change.

---

## Comment 9

> Username: sdebionne  
> Created at: 2020-04-06 07:31:14 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-609616784  

Maybe I mislead the discussion when I said MPL Sequence, it should read Type Sequence (`mp11::mp_list` is used these days).  
I am afraid that changing the template parameter from a single type sequence to parameter pack increase compile time, is that something to worry about?

---

## Comment 10

> Username: pdimov  
> Created at: 2020-04-06 07:51:05 UTC  
> Url: https://github.com/boostorg/gil/issues/232#issuecomment-609633512  

Hard to say without a concrete example, but my guess would be that it won't. In either case, MPL is so much slower to compile than mp11 that it won't really matter.
