# #150 - rename adaptive_storage [Closed]

> Username: HDembinski  
> Created at: 2019-02-05 21:02:09 UTC  
> Updated at: 2019-02-06 22:54:13 UTC  
> Closed at: 2019-02-06 22:54:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/150  

adaptive_storage sounds too similar to storage_adaptor. The latter is difficult to rename, so I need to rename adaptive_storage. Options:  
* dynamic_storage  
* elastic_storage  
* growing_storage  
  
I slightly prefer elastic_storage.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-02-05 21:05:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460803102  

@jpivarski @henryiii Any thoughts? Names are hard and input is appreciated!

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-02-05 21:06:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460803625  

adaptive_storage is the storage where the cell capacity grows as needed, so that it is able to storage larger and larger integers.

---

## Comment 3

> Username: jpivarski  
> Created at: 2019-02-05 21:11:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460805313  

"Elastic" is hip (or was so recently; the name has died down a little). What your adaptive_storage does reminds me of "BigInt" (for computations) and "varint" (for serialization). It's not exactly either of these in that the whole array gets promoted (to maintain random access), but it's in the same spirit.

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-02-05 21:22:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460808681  

I like varint_storage, but it is not a perfect fit. The adaptive_storage does not only use integers, it is also switches to a double value when you scale it with a real number. That's why "growing_storage" is also not a perfect fit.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-02-05 21:24:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460809562  

I want to avoid dynamic_storage, because `static` and `dynamic` remind me too much of `static_cast` and `dynamic_cast`.  
Ah, my Boost friends would probably call this type_erased_storage.

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-02-05 21:25:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460809880  

https://www.boost.org/doc/libs/1_67_0/libs/range/doc/html/range/reference/adaptors/reference/type_erased.html  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost_typeerasure.html

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-02-05 21:29:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460810847  

virtual_storage?

---

## Comment 8

> Username: henryiii  
> Created at: 2019-02-05 21:30:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460811386  

I like `elastic_storage` best, I believe.

---

## Comment 9

> Username: henryiii  
> Created at: 2019-02-05 21:31:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460811669  

Oh, wait, nevermind, I thought it was something else. I'll give input soon.

---

## Comment 10

> Username: jpivarski  
> Created at: 2019-02-05 21:32:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460811925  

No, virtual_storage would be if it was lazily generated, which it's not. When I checked Wikipedia for "type erasure," it meant the reverse of "type inference," like `auto`. That's not what it is, either.  
  
If it were me, I'd go with elastic. It's the best word proposed so far, in my opinion.

---

## Comment 11

> Username: henryiii  
> Created at: 2019-02-05 21:49:15 UTC  
> Updated at: 2019-02-05 21:52:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460817249  

Okay, I was thinking this was about axis, not storage. Here's my input:  
  
* `elastic_storage`: This seems to be pretty good. As long as the reader doesn't miss the "storage" part, and understands this does not mean an axis will grow. `growing_storage` is similar, but has that problem even worse.  
* `type_erasure_storage`: Implies wrong concept. This would mean I can mix types with something like `std::any`.  
* `unlimited_storage`: This could work, but I assume you stop at 64 bits?  
* `flexible_storage`: Might also work.  
* `dynamic_storage`: Too similar to type_erasure, it sounds like you are able to put in different types and cast them.

---

## Comment 12

> Username: henryiii  
> Created at: 2019-02-05 21:52:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460818148  

PS: Does each counter change as it hits its limit? I think `flexible_storage` or `flexible_numeric_storage` might be an option. Adding numeric makes it clear that this is just meant to change numeric types, but it probably too wordy.

---

## Comment 13

> Username: jpivarski  
> Created at: 2019-02-05 21:53:21 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460818559  

Actually, I also like "flexible_storage". Maybe a little more than "elastic_storage" (because "elastic," as it was used in the past few years, meant starting up and shutting down servers to mean load demands). Smoothly transitioning from int8 through all integers to floating point is "flexible!"

---

## Comment 14

> Username: HDembinski  
> Created at: 2019-02-06 08:59:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460947036  

Thank you guys, this is great input! So you guys prefer flexible_storage, which is a cool name. I like unlimited_storage. It is a powerful name for a powerful storage. The storage does not stop at 64 bits. If you are about to overflow 64 bits, the storage switches to a BigInt type, which is limited only by available memory. So, the name actually fits quite well. The marketing department of Boost.Histogram likes this choice.

---

## Comment 15

> Username: HDembinski  
> Created at: 2019-02-06 09:01:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460947588  

And you are right, I did some more research yesterday, and type erasure does not quite fit.

---

## Comment 16

> Username: henryiii  
> Created at: 2019-02-06 09:02:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460947782  

Given that it does switch to bigint, I like `unlimited_storage`. (I still like `flexible_storage` as well). `flexible_unlimited_numeric_storage` would be the most accurate description of what it does ;P.

---

## Comment 17

> Username: HDembinski  
> Created at: 2019-02-06 11:47:09 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460995279  

True, but since you will not really interact with this storage a lot as a user, it is ok to have a less descriptive shorter name.

---

## Comment 18

> Username: HDembinski  
> Created at: 2019-02-06 11:47:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460995445  

I also like flexible_storage, because the storage also switches to double if it is scaled by a real number.

---

## Comment 19

> Username: HDembinski  
> Created at: 2019-02-06 11:48:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460995505  

I just think unlimited_storage sounds way cooler :)

---

## Comment 20

> Username: henryiii  
> Created at: 2019-02-06 12:04:29 UTC  
> Updated at: 2019-02-06 12:04:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-460999969  

> you will not really interact with this storage a lot as a user, it is ok to have a less descriptive shorter name   
  
That statement is backwards, IMO. If you don't interact with something often, you need a longer, more descriptive name and you don't need to type as often so long is okay. `flexible_unlimited_storage` may not really be that bad...

---

## Comment 21

> Username: jpivarski  
> Created at: 2019-02-06 12:06:30 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-461000511  

My MINOS optimization has jumped to a new minimum twice: from adaptive_storage to flexible_storage, and now to unlimited_storage. Currently, I like unlimited_storage best. It's descriptive, doesn't conflict with a different concept, and explains why you ultimately want this.

---

## Comment 22

> Username: HDembinski  
> Created at: 2019-02-06 12:26:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/150#issuecomment-461005594  

@henryiii I see your point in general, but the concept of a storage is very well defined in the context of Boost.Histogram. And there are only two. There is no need to make the name more descriptive. And the users will not even touch the name. There is an alias, default_storage, which points to unlimited_storage. unlimited_storage is simply what you get if you do not explicitly want a different storage. Under these circumstances, I think the shorter name is admissible.
