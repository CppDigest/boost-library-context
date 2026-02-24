# #105 - boost::property_tree::read_json takes a long time [Open]

> Username: WuDaWeiCTO  
> Created at: 2023-03-13 03:36:29 UTC  
> Updated at: 2024-06-14 10:36:08 UTC  
> Url: https://github.com/boostorg/property_tree/issues/105  

boost1.56.0  boost::property_tree::read_json  takes a long time 16ms, and occasionally takes a short time 0ms

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-17 14:47:21 UTC  
> Url: https://github.com/boostorg/property_tree/issues/105#issuecomment-1816559216  

@WuDaWeiCTO, could you please provide more context for this? It could be an OS issue if you're attempting to read a JSON file from disk. Do you have a reproducible example code?

---

## Comment 2

> Username: nvkhoi112358  
> Created at: 2024-06-14 10:36:08 UTC  
> Url: https://github.com/boostorg/property_tree/issues/105#issuecomment-2167744624  

I think boost add explicit Json since 1.75.0 https://www.boost.org/doc/libs/1_75_0/libs/json/doc/html/index.html  
In my case, it is 4x faster.  
  
JSON parser through ptree is a very bad idea.
