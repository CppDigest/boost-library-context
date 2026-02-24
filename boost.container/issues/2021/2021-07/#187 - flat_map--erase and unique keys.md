# #187 - flat_map::erase and unique keys [Closed]

> Username: mglisse  
> Created at: 2021-07-05 04:05:17 UTC  
> Updated at: 2021-08-07 22:38:35 UTC  
> Closed at: 2021-08-07 22:38:35 UTC  
> Url: https://github.com/boostorg/container/issues/187  

flat_map is documented as supporting unique keys, but flat_map::erase(key_type) is documented as erasing all the elements with that key (as if there could be several) and indeed the code wastes time looking for a range of equal keys after it has found one equal key.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-07 22:34:43 UTC  
> Url: https://github.com/boostorg/container/issues/187#issuecomment-894713934  

Thanks for the report, the same optimization can be done for map/set/flat_set.
