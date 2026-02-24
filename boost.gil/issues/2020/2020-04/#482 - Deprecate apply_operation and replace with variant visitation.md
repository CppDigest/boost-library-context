# #482 - Deprecate apply_operation and replace with variant visitation [Closed]

> Username: mloskot  
> Created at: 2020-04-17 17:21:49 UTC  
> Updated at: 2022-06-26 11:21:18 UTC  
> Closed at: 2022-06-26 11:21:18 UTC  
> Url: https://github.com/boostorg/gil/issues/482  

Follow-up to https://github.com/boostorg/gil/pull/474#discussion_r409495768 between @sdebionne and @simmplecoder and @mloskot   
  
> `apply_operation` should be marked [[deprecated]] and provided only for backward compatibility. Just use visit in modern code (no limit on arity, etc...)  
  
> there is BOOST_NO_CXX11_DECLTYPE vs BOOST_NO_CXX11_DECLTYPE_N3276
