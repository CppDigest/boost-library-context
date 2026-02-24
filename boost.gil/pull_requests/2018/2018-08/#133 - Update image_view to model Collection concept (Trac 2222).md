# #133 Update image_view to model Collection concept (Trac 2222) [Merged]

> Username: mloskot  
> Created at: 2018-08-29 20:12:10 UTC  
> Updated at: 2018-08-30 14:05:56 UTC  
> Merged at: 2018-08-30 14:01:57 UTC  
> Closed at: 2018-08-30 14:01:57 UTC  
> Url: https://github.com/boostorg/gil/pull/133  

* Applies patch from John Femiani submitted via Trac - https://svn.boost.org/trac10/ticket/2222 description:  
  
>   It would be convenient if GIL views modeled `ReversibleCollection` concept.  
>   In fact they almost do already. Without modeling this concept, it is  
>   hard to use an image view with boost::range algorithms.  
  
* Add related image_view concepts for [Collection, ForwardCollection, ReversibleCollection](https://www.boost.org/doc/libs/1_68_0/libs/utility/Collection.html).  
* Add tests for the new concepts.  
* Add run-time tests for the new image_view methods.  
  
### References  
  
* https://svn.boost.org/trac10/ticket/2222  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-08-29 21:27:28 UTC  
> Url: https://github.com/boostorg/gil/pull/133#issuecomment-417111769  

I must say I don't quite follow what is going on. The code changes look good but in the tests I don't see anything new. Am I wrong?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-08-30 03:59:58 UTC  
> Updated_at: 2018-08-30 07:29:59 UTC  
> Url: https://github.com/boostorg/gil/pull/133#issuecomment-417182549  

Hmm, as far as I see, I have   
* added new file [test/image_view/collection.cpp](https://github.com/boostorg/gil/blob/b1eaa7ea905c5350191fe7afe656ce561fc3ec33/test/image_view/collection.cpp)  
* updated [test/image_view/image_view_concepts.cpp]  
  
https://github.com/boostorg/gil/blob/b1eaa7ea905c5350191fe7afe656ce561fc3ec33/test/image_view/image_view_concepts.cpp#L18-L20

---

## Comment 3

> Username: chhenning  
> Created_at: 2018-08-30 12:54:26 UTC  
> Url: https://github.com/boostorg/gil/pull/133#issuecomment-417308907  

I think I wasn't clear. Sorry.  
What in the test cases in image_view/collection.cpp shows off a new feature of image_view?

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-08-30 13:09:56 UTC  
> Url: https://github.com/boostorg/gil/pull/133#issuecomment-417313496  

The new feature is addition of methods that make the `image_view` model the three concepts:  [Collection, ForwardCollection, ReversibleCollection](https://www.boost.org/doc/libs/1_68_0/libs/utility/Collection.html).  
  
The `test/image_view/collection.cpp` includes basic test cases that exercise all the methods corresponding to the Collection concept and its two refinements.  
  
Specifically, the four new additions are tested:  
  
https://github.com/boostorg/gil/blob/b1eaa7ea905c5350191fe7afe656ce561fc3ec33/test/image_view/collection.cpp#L60-L68  
  
https://github.com/boostorg/gil/blob/b1eaa7ea905c5350191fe7afe656ce561fc3ec33/test/image_view/collection.cpp#L80-L91  
  
https://github.com/boostorg/gil/blob/b1eaa7ea905c5350191fe7afe656ce561fc3ec33/test/image_view/collection.cpp#L46-L58  
  
Does it answer your question?

---

## Comment 5

> Username: chhenning  
> Created_at: 2018-08-30 13:20:51 UTC  
> Updated_at: 2018-08-30 13:28:11 UTC  
> Url: https://github.com/boostorg/gil/pull/133#issuecomment-417316723  

OK. Now we have member functions like, empty(), back(), etc. I thought there was empty() already!

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-08-30 14:01:31 UTC  
> Url: https://github.com/boostorg/gil/pull/133#issuecomment-417330179  

Yes, there is no `empty` etc. in the current [image_view.hpp](https://github.com/boostorg/gil/blob/4500543a74232f843abaacfb796f3e6beaad1303/include/boost/gil/image_view.hpp)  :-)

---
