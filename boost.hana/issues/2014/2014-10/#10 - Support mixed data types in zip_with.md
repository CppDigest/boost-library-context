# #10 - Support mixed data types in zip_with [Closed]

> Username: ldionne  
> Created at: 2014-10-05 00:23:37 UTC  
> Updated at: 2017-11-25 19:57:11 UTC  
> Closed at: 2017-11-25 19:56:59 UTC  
> Url: https://github.com/boostorg/hana/issues/10  

Currently, it only makes sense to use `zip_with` with objects that all have the same data type, since only the data type of the first object is used for dispatching. It might make sense to allow objects of different data types, but I'm not sure how that could be implemented. Questions that have to be answered and possible issues include:  
- Would it require multiple dispatch at the data type level?  
- Would it actually be useful? How could one implement a `zip_with` not knowing the data types of the objects?

---

## Comment 1

> Username: ldionne  
> Created at: 2017-11-25 19:56:59 UTC  
> Url: https://github.com/boostorg/hana/issues/10#issuecomment-346962418  

Closing as too esoteric. Will reopen if someone wants to zip sequences with different tags.
