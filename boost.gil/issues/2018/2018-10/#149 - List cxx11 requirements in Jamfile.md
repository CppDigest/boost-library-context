# #149 - List cxx11 requirements in Jamfile [Closed]

> Username: mloskot  
> Created at: 2018-10-05 20:53:36 UTC  
> Updated at: 2019-04-13 21:24:59 UTC  
> Closed at: 2019-04-13 21:24:59 UTC  
> Url: https://github.com/boostorg/gil/issues/149  

For example, `mp11/test/Jamfile`:  
  
```  
project :  
  requirements [ requires cxx11_variadic_templates cxx11_template_aliases cxx11_decltype cxx11_hdr_tuple ] ;  
```  
  
Alternatively, as explained by @pdimov on cpplang.slack.com:  
  
> if you add `cxxstd=11`, they are satisfied, if you add `cxxstd=03`, they aren't  
> so if your test requires f.ex. `decltype`, it's run with `cxxstd=11` and not run with `cxxstd=03`  
>but you can have tests that don't need `decltype`, which are run under `cxxstd=03` as well, in principle  
  
This will also help to skip the always failing builds in the regression waterfall ie. VS 2005, 2008, 2010, 2012, 2013:  
  
![image](https://user-images.githubusercontent.com/80741/46559516-e4bb8180-c8f0-11e8-8dac-8847ac17e381.png)

---

## Comment 1

> Username: mloskot  
> Created at: 2019-01-11 21:01:07 UTC  
> Url: https://github.com/boostorg/gil/issues/149#issuecomment-453655070  

Asked via ML about [Skipping regression builds for unsupported toolsets](https://lists.boost.org/Archives/boost/2019/01/244816.php)

---

## Comment 2

> Username: mloskot  
> Created at: 2019-04-13 21:24:59 UTC  
> Url: https://github.com/boostorg/gil/issues/149#issuecomment-482890028  

This has been fixed by #260
