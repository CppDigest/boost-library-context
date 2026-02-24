# #1 [doc] Clarify that iterator_range and sub_range may also model refinements of Forward Range concept. [Closed]

> Username: m-w-a  
> Created at: 2013-12-29 02:27:58 UTC  
> Updated at: 2014-08-13 05:05:52 UTC  
> Closed at: 2014-06-09 22:27:51 UTC  
> Url: https://github.com/boostorg/range/pull/1  



---

## Comment 1

> Username: HighCommander4  
> Created_at: 2014-08-13 05:05:52 UTC  
> Url: https://github.com/boostorg/range/pull/1#issuecomment-52010247  

I agree that it is clear that if iterator_range is documented as requiring ForwardIterators as its input, then it also accepts BidirectionalIterators and RandomAccessIterators.  
  
What is not clear is that the resulting range class models BidirectionalRange if the inputs model BidirectionalIterators, and RandomAccessRange if the inputs model RandomAccessIterators. I could easily imagine a simpler iterator_range class which always just modelled ForwardRange even if it were given more refined iterators. Therefore, I think this is worth documenting.

---
