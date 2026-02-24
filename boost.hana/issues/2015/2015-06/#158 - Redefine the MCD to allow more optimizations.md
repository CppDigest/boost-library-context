# #158 - [Iterable] Redefine the MCD to allow more optimizations [Closed]

> Username: ldionne  
> Created at: 2015-06-28 10:32:35 UTC  
> Updated at: 2015-11-12 04:27:54 UTC  
> Closed at: 2015-11-12 04:27:54 UTC  
> Url: https://github.com/boostorg/hana/issues/158  

The current MCD of `Iterable` is very easy to fulfil, but it also leads to bad implementations for several algorithms. It would be better to redefine it (or to define two alternate MCDs) that are more efficient. Also note that we should remove the `tail` function, which is now superseded by `drop_front`.  
  
Related to #66
