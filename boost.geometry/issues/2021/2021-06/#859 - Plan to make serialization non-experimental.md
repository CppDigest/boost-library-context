# #859 - Plan to make serialization non-experimental [Open]

> Username: maliberty  
> Created at: 2021-06-01 13:48:38 UTC  
> Updated at: 2021-06-13 09:20:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/859  

Is there a plan to make the rtree serialization non-experimental?  
The code itself seems to have been around for quite a while.  
Are there any issues to be aware of in using this code?

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-06-01 18:39:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-852357132  

Yes, there was always a plan but there was not enough free time. The serialization support is still experimental and conditionally enabled with a macro definition because it's implemented in the internals of the R-tree. To be officially released it should be rewritten, implemented outside of the R-tree and optionally included because Boost.Geometry should not depend on Boost.Serialization by default.

---

## Comment 2

> Username: maliberty  
> Created at: 2021-06-11 03:16:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-859231691  

How would you implement it outside the rtree?   It depends on the internals of the rtree which I don't see a public API exposing.

---

## Comment 3

> Username: maliberty  
> Created at: 2021-06-11 03:33:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-859237418  

As an experiment I simply got all the elements from begin() to end() into a vector and serialized that on save.  On load I just bulk inserted them into a new tree.  You get a tree but when you query it the elements come out in different orders, which in my application is bad.  Is there a way to reconstruct the exact tree from the public API?

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-06-11 09:11:59 UTC  
> Updated at: 2021-06-11 09:35:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-859414982  

> Is there a way to reconstruct the exact tree from the public API?  
  
No, there is no public API for this. And yes, the placement of elements in the tree depends on how the tree was created.  
  
Out of curiosity, why having the elements in a different order is it bad in your app? The placement is irrelevant for the R-tree, as well as is the order of elements in the result of the spatial query. It's because you could use a different algorithm for creating the tree or the elements could be differently ordered before putting them into the tree. In certain cases you could get faster tree by rebuilding it using a different algorithm. Etc.

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-06-11 09:34:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-859448332  

> How would you implement it outside the rtree? It depends on the internals of the rtree which I don't see a public API exposing.  
  
There is non-documented, internal API allowing to access the internals of the R-tree from the outside, see:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/rtree.hpp#L314-L318  
https://github.com/boostorg/geometry/tree/develop/include/boost/geometry/index/detail/rtree/utilities  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/serialization.hpp  
  
Actually I see that I already moved the serialization support into the file above so it's no longer a member function of the rtree and technically could be included separately: https://github.com/boostorg/geometry/commit/64730165b5ecfc9d9f7e94432f2bce4f81ae2373  
But this wasn't released. I probably implemented exactly the same functionality as before but there are more things that needs to be done before this code is ready to be released. There are many TODOs in this file.

---

## Comment 6

> Username: maliberty  
> Created at: 2021-06-13 04:07:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-860147836  

The elements are processed in the order returned by the query.  The  
algorithm (vlsi routing) depends on the order in which objects are  
processed.  Changing the order changes the result.  I realize that ordering  
is arbitrary but what I want is to serialize and restart for faster  
debugging (it may take hours, optimized, to reach the problem).   If that  
process changes the behaviour then the debugging won't really work.  
Another use model is to serialize and distribute but I still want to get  
the same result as when not distributing/serializing.  I can avoid this by  
adding sorting but that is an extra cost for no real end user benefit.  In  
short, I'd like to freeze and restore exactly.  
  
Matt  
  
On Fri, Jun 11, 2021 at 2:35 AM Adam Wulkiewicz ***@***.***>  
wrote:  
  
> How would you implement it outside the rtree? It depends on the internals  
> of the rtree which I don't see a public API exposing.  
>  
> There is non-documented, internal API allowing to access the internals of  
> the R-tree from the outside, see:  
>  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/rtree.hpp#L314-L318  
>  
> https://github.com/boostorg/geometry/tree/develop/include/boost/geometry/index/detail/rtree/utilities  
>  
> https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/detail/serialization.hpp  
>  
> Actually I see that I already moved the serialization support into the  
> file above so it's no longer a member function of the rtree and technically  
> could be included separately: 6473016  
> <https://github.com/boostorg/geometry/commit/64730165b5ecfc9d9f7e94432f2bce4f81ae2373>  
> But this wasn't released. I probably implemented exactly the same  
> functionality as before but there are more things that needs to be done  
> before this code is ready to be released. There are many TODOs in this file.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/geometry/issues/859#issuecomment-859448332>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AAFZ5KSBEMCFN4F7QJW4ZYTTSHKEJANCNFSM454W3KJA>  
> .  
>

---

## Comment 7

> Username: awulkiew  
> Created at: 2021-06-13 09:20:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/859#issuecomment-860180041  

Thanks for the explanation.  
  
I'll treat this issue as a feature request but I don't know when I'll look into this.
