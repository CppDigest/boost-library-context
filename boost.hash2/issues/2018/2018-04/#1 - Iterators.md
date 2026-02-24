# #1 - Iterators [Closed]

> Username: breese  
> Created at: 2018-04-27 10:46:34 UTC  
> Updated at: 2018-05-27 13:11:12 UTC  
> Closed at: 2018-05-27 13:11:12 UTC  
> Url: https://github.com/boostorg/hash2/issues/1  

The `update()` function of the various hash functions currently takes a pointer-size pair. Can this be changed to, or extended with, an iterator pair?  
  
The reason i am asking is that I would like to transform the input before I calculate its hash value, and I would like to avoid having to store a copy of the transformed input. This can be done by using a transforming iterator.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-04-27 15:24:58 UTC  
> Url: https://github.com/boostorg/hash2/issues/1#issuecomment-385004251  

The HashAlgorithm concept is low-level and one design goal is to make writing hash algorithms easy by not burdening them with complexity.  
  
The way to pass an iterator range to a hash algorithm `h` is `hash_append_range( h, first, last );`.

---

## Comment 2

> Username: breese  
> Created at: 2018-05-27 13:11:12 UTC  
> Url: https://github.com/boostorg/hash2/issues/1#issuecomment-392330050  

`hash_append_range` will work for my purposes (or better yet repeated calls to `hash_append` so I do not have to create a transforming iterator in the first place.)
