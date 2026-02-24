# #36 - Consider using BOOST_[UN]LIKELY in conditional branches [Closed]

> Username: badair  
> Created at: 2017-03-11 22:43:28 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2017-04-09 20:51:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/36  

... In both conditional operators and if/else blocks. This can yield better code generation on some platforms.  
  
http://www.boost.org/doc/libs/1_63_0/libs/config/doc/html/boost_config/boost_macro_reference.html  
http://stackoverflow.com/questions/15468042/how-far-does-gccs-builtin-expect-go

---

## Comment 1

> Username: robertramey  
> Created at: 2017-04-09 20:51:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/36#issuecomment-292812065  

I would be extremely unlikely to complicate code even further by including this information.  If I were to actually profile the code I might change my mind but I sort of doubt it.  So I'm going to close this.  I'll add to a list of pending ideas/enhancements.
