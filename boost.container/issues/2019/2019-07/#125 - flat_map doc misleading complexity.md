# #125 - flat_map doc misleading complexity [Closed]

> Username: mglisse  
> Created at: 2019-07-17 21:51:33 UTC  
> Updated at: 2020-07-13 10:33:11 UTC  
> Closed at: 2020-07-13 10:32:08 UTC  
> Url: https://github.com/boostorg/container/issues/125  

Hello,  
the documentation of some flat_map operations has a misleading complexity guarantee. For instance, `operator[]` or `insert_or_assign` say "logarithmic", but while the number of comparisons is logarithmic, the number of move/copy can be linear. `emplace` kind of acknowledges this ("Logarithmic search time plus linear insertion to the elements with bigger keys than x"), although it is still wrong if the capacity is exceeded.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-07-13 10:33:10 UTC  
> Url: https://github.com/boostorg/container/issues/125#issuecomment-657478872  

Many thanks for the report!
