# #137 Update boost_has_nl_types_h.ipp [Merged]

> Username: mclow  
> Created at: 2017-05-19 18:39:34 UTC  
> Updated at: 2017-05-20 10:28:38 UTC  
> Merged at: 2017-05-20 10:28:38 UTC  
> Closed at: 2017-05-20 10:28:38 UTC  
> Url: https://github.com/boostorg/config/pull/137  

clang 5 complains that comparing a `nl_catd` to an integer is not allowed.  
  
./boost_has_nl_types_h.ipp:20:11: error: ordered comparison between pointer and zero ('nl_catd' (aka '__nl_cat_d *') and 'int')  
   if(cat >= 0) catclose(cat);

---
