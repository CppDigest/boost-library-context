# #67 Cease dependence on ContainerHash [Merged]

> Username: Kojoley  
> Created at: 2020-05-25 22:33:09 UTC  
> Updated at: 2021-06-23 14:55:10 UTC  
> Merged at: 2021-06-23 14:42:12 UTC  
> Closed at: 2021-06-23 14:42:12 UTC  
> Url: https://github.com/boostorg/utility/pull/67  

by local `hash_range` forward declaration

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-05-25 22:41:17 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-633734314  

It is needed for `hash_range`.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-05-25 22:47:54 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-633735400  

Indeed. It is been not the first time I got hit by broken two-phase lookup on one famous platform...

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-05-25 23:21:16 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-633740513  

I have pulled forward declaration and reopened the PR.

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-05-25 23:56:58 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-633746029  

I don't think duplicating the forward declaration is the right thing to do, that's why there is a forward header in the first place. But I'll let @mclow decide.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-05-26 00:01:58 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-633746834  

The thing is that you need to download the whole library just to use the forward declaration from it.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2021-06-04 01:57:20 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-854299178  

Ping? I found out that Boost.Array does it already for a long time https://github.com/boostorg/array/commit/cb4df41bd6bb64eead5e8d38b49716e7b681204c

---

## Comment 7

> Username: Kojoley  
> Created_at: 2021-06-20 14:16:48 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864561814  

@pdimov you've done the same change to Array in https://github.com/boostorg/array/commit/cb4df41bd6bb64eead5e8d38b49716e7b681204c, could you please take authority over this PR since I'm not getting any response from maintainers?

---

## Comment 8

> Username: pdimov  
> Created_at: 2021-06-20 16:04:18 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864575968  

I see nothing wrong with this PR (obviously) but I can't take authority over it, sorry. :-)

---

## Comment 9

> Username: mclow  
> Created_at: 2021-06-20 16:22:20 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864578551  

My feeling here is that the title of the PR is misleading: "Cease dependence on ContainerHash".  
The dependency is not removed, just the include.  And by replicating the declaration, the code is made more brittle - if `hash_range` changes in the future, then user's code will (if they are lucky) fail to link.  If they're unlucky, then their code will crash at runtime.

---

## Comment 10

> Username: mclow  
> Created_at: 2021-06-20 16:28:25 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864579320  

I guess I'm not understanding the rationale here. What is the goal of this PR?   
I doubt that it's compile-time performance, since `hash_fwd.hpp` is very small.

---

## Comment 11

> Username: Kojoley  
> Created_at: 2021-06-20 16:31:15 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864579659  

> My feeling here is that the title of the PR is misleading: "Cease dependence on ContainerHash".  
> The dependency is not removed, just the include.  
  
The dependency on ContainerHash is removed for anyone who do not use `hash_range`.  
  
> And by replicating the declaration, the code is made more brittle - if `hash_range` changes in the future, then user's code will (if they are lucky) fail to link. If they're unlucky, then their code will crash at runtime.  
  
I doubt that, but anyway that's not a problem at all, both libraries are in Boost and could be fixed quickly.  
  
> I guess I'm not understanding the rationale here. What is the goal of this PR?  
> I doubt that it's compile-time performance, since `hash_fwd.hpp` is very small.  
  
To not pull unneeded repositories https://github.com/boostorg/utility/blob/9ad7e519129c5e557b155b37c7fdb806d03609ca/CMakeLists.txt#L18

---

## Comment 12

> Username: pdimov  
> Created_at: 2021-06-20 16:33:14 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864579901  

It removes the physical dependency. To include the header, the contents of ContainerHash need to be physically present. This means that in the modular case the repo needs to be checked out.

---

## Comment 13

> Username: Lastique  
> Created_at: 2021-06-20 18:27:30 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864593797  

FWIW, in order to improve dependencies in general, I would've preferred if Boost.StringView was moved into its own submodule.  
  
(As I noted before, I don't consider replicating forward declarations a good practice - not in terms of stability, scalability or maintainability.)

---

## Comment 14

> Username: Kojoley  
> Created_at: 2021-06-20 22:23:48 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864619487  

> (As I noted before, I don't consider replicating forward declarations a good practice - not in terms of stability, scalability or maintainability.)  
  
Array is stable for these 7 years the change was committed. Forward declarations are the core part that makes C++ scalable. One change per 7 years is pretty maintainable. Also, your equation does not include users, but it is seems like a general line in the Boost lately.

---

## Comment 15

> Username: pdimov  
> Created_at: 2021-06-20 22:39:06 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-864621015  

Libraries such as ContainerHash (and Serialization, and Json) should, ideally, provide a documented way of forward-declaring the necessary minimum that enables adding support for them without including a header.

---

## Comment 16

> Username: mclow  
> Created_at: 2021-06-23 14:43:43 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-866900101  

> Libraries such as ContainerHash (and Serialization, and Json) should, ideally, provide a documented way of forward-declaring the necessary minimum that enables adding support for them without including a header.  
  
That's what I thought that the point of 'hash_fwd.hpp' was.  
Maybe header file shouldn't belong in 'container_hash'

---

## Comment 17

> Username: Kojoley  
> Created_at: 2021-06-23 14:55:10 UTC  
> Url: https://github.com/boostorg/utility/pull/67#issuecomment-866909742  

Thanks for merging.

---
