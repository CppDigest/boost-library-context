# #44 - Unused local type error with warnings-as-errors=on variant=release [Closed]

> Username: grisumbras  
> Created at: 2021-10-19 11:01:41 UTC  
> Updated at: 2021-10-19 14:12:35 UTC  
> Closed at: 2021-10-19 14:12:25 UTC  
> Url: https://github.com/boostorg/move/issues/44  

2af840af introduced a type alias [here](https://github.com/boostorg/move/blob/2af840afc1288968005f3f8c307f22fc13cd0edc/include/boost/move/algo/detail/adaptive_sort_merge.hpp#L1026). That type alias is only used in a later `BOOST_ASSERT` and thus triggers  a "typedef 'difference_type' locally defined but not used" warning, which is turned into an error when `warnings-as-errors=on`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-10-19 14:12:35 UTC  
> Url: https://github.com/boostorg/move/issues/44#issuecomment-946766357  

Thanks for the report!
