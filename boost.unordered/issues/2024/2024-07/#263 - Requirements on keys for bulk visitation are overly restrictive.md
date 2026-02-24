# #263 - Requirements on keys for bulk visitation are overly restrictive [Open]

> Username: joaquintides  
> Created at: 2024-07-13 09:46:31 UTC  
> Updated at: 2024-07-13 09:49:39 UTC  
> Url: https://github.com/boostorg/unordered/issues/263  

Currently, bulk visitation requires that  
  
>[f]or K = std::iterator_traits<FwdIterator>::value_type, either K [be] key_type or else Hash::is_transparent and Pred::is_transparent [be] valid member typedefs.   
  
This precludes the scenario where the keys are implicitly convertible to `key_type`, which seems to be overly restrictive. The drawback of allowing this is that conversion from `std::iterator_traits<FwdIterator>::value_type` to `key_type` will happen more than once for each key (twice, to be precise).
