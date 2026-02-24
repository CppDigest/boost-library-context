# #73 - Improve accumulate() performance with InputIterator [Closed]

> Username: kylelutz  
> Created at: 2014-03-19 15:11:57 UTC  
> Updated at: 2014-04-11 05:52:19 UTC  
> Closed at: 2014-04-11 05:35:20 UTC  
> Url: https://github.com/boostorg/compute/issues/73  

Right now, accumulate() and reduce() have a optimized version that is used when called with `buffer_iterator`s. Update the code so that the optimized version works with all `InputIterator` types.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-11 05:35:20 UTC  
> Url: https://github.com/boostorg/compute/issues/73#issuecomment-40172176  

Fixed in acb2188382584b198b76c6fea69c9815abc39f28.
