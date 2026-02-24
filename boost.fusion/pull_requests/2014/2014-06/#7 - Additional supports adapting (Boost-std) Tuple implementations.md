# #7 Additional supports adapting (Boost|std) Tuple implementations [Merged]

> Username: Flast  
> Created at: 2014-06-09 13:01:57 UTC  
> Updated at: 2014-06-14 07:13:47 UTC  
> Merged at: 2014-06-09 13:11:03 UTC  
> Closed at: 2014-06-09 13:11:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/7  

Those changes introduce following implementations.  
  
1) fusion::convert<boost_tuple_tag> and fusion::convert<std_tuple_tag>  
2) mpl::clear<boost::tuple<...>> and mpl::clear<std::tuple<...>>  
  
Those changes required by Fusion-based zip_iterator: see https://svn.boost.org/trac/boost/ticket/7526 .

---

## Comment 1

> Username: djowel  
> Created_at: 2014-06-09 13:10:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/7#issuecomment-45488673  

Looks good! Thanks for this patch!

---

## Comment 2

> Username: Flast  
> Created_at: 2014-06-09 13:16:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/7#issuecomment-45489110  

Wow! Thank you for quite quick merging!

---
