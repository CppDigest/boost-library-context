# #238 - question about [Closed]

> Username: greg7mdp  
> Created at: 2024-10-30 19:59:47 UTC  
> Updated at: 2024-11-03 10:25:17 UTC  
> Closed at: 2024-11-03 10:25:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/238  

Just curious, do we really need these masks like `& block_ctrl::size_mask` [here](https://github.com/boostorg/interprocess/blob/1739eeb8871487074e1843d88e43dd2ae5f0786b/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp#L416) for example?  
  
Since we divide by `Alignment` which is `4` or `8`, those high-order bits should already be zero, right?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-11-03 10:25:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/238#issuecomment-2453374661  

Because some boost libraries that depend on Interprocess use very high warning levels that are triggered if explicit masks are not used (especially in older GCC versions). It was easier to be too explicit instead of selectively disabling warnings for those cases.
