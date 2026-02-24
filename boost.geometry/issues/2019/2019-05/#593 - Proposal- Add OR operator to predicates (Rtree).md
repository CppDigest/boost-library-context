# #593 - Proposal: Add OR operator to predicates (Rtree) [Open]

> Username: FlorentTomi  
> Created at: 2019-05-24 07:37:45 UTC  
> Updated at: 2020-04-19 03:18:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/593  

Hello,  
  
Using Rtree, I fell into the case where I need to know whether a box is within another box OR overlaps it. The current implementation doesn't allow this operation.   
In my understanding, I can't see any reason not to allow it, as AND and NOT are allowed.  
We can't even use De Morgan's law (`A || B = !(!A && !B)`) as we can't negate a whole expression (AND operator returns a boost::tuples::cons and not another predicate with the NOT operator implemented).  
  
Maybe there is a limitation I can't see, if there is, I'll be happy to know it ;)  
Thanks !

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-05-24 10:18:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/593#issuecomment-495560310  

Hi,  
  
Thanks for the suggestion. This was something I was considering in the past.  
  
Ok so there are 2 issues here: your problem (1) and general predicates expression support in rtree (2).  
  
1. This is trivial to implement right now and I think this solution would be faster than complex expression support in the rtree. So you can use predicate for which both overlaps and within are true and then filter the results like that:  
```  
rtree.query(bgi::intersects(box), std::back_inserter(result));  
for (value const& v : result)  
    if (bg::overlaps(v, box))  
       // ...  
    else if (bg::within(v, box))  
        // ...  
```  
  
2. Yes, currently predicates concatenated with `&&` are only a syntactic sugar allowing to express a list of predicates nicely in the code. It's not full logical expression support.  
  
I've choosen to support only AND because this is the logical operation which narrows the search down, so adding more predicates allows to prune even more nodes while traversing the tree structure. With the full expression support and OR in particular one would have to analyze the subexpressions and at each node check which lower-level subtrees has to be traversed as usual but then also analyze which subexpressions theoretically will never be TRUE at lower levels to not check them in the future (because with OR one can be TRUE or another).  
  
This could be implemented in several ways and I'm not convinced that this would be faster than several subsequent calls to `query()` as they are now. I'm also not sure if it would be possible to analyze the expression on such level that it would allow to optimize expressions like the one above (addition of 3rd predicate to replace 2 other ones when in them the same box is passed, etc.). Even if possible it would significantly increase compilation time. Last but not least, in practice you can do something like in the code above and I doubt that many users would need full expression support. So it would require a lot of work and I think the practical impact would be very small.  
  
Testing performance of full expression support would be interesting but I'm afraid it would have very low priority on my to-do list because of the reasons mentioned above. If I decided to play with it it'd be driven only by curiosity (unless I'm convinced otherwise) and there are also other interesting things to do which are more practical. But if you like feel free to implement it. :)

---

## Comment 2

> Username: FlorentTomi  
> Created at: 2019-05-24 12:14:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/593#issuecomment-495598888  

Thank for the suggestion for my problem (I will certainly do that).  
  
The main point of the issue is the clarity of code. I am very pleased by the fact that I can combine predicates. I honestly think it wouldn't matter performance-wise (if well implemented).
