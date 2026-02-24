# #236 - submodule refs broken [Closed]

> Username: zoeeer  
> Created at: 2018-12-27 08:56:15 UTC  
> Updated at: 2019-06-26 15:43:26 UTC  
> Closed at: 2019-06-26 15:43:26 UTC  
> Url: https://github.com/boostorg/boost/issues/236  

I need to use the boost-1.64.0 version, but most of the submodule refs are not available any more.  
```  
> git submodule update --init --recursive  
error: no such remote ref d022c85123683138ebec9149f0dc9aec6f3839cf  
Fetched in submodule path 'libs/accumulators', but it did not contain d022c85123683138ebec9149f0dc9aec6f3839cf. Direct fetching of that commit failed.  
```  
This is the "libs/accumulators" module. If I skip this one, others fail as well.  
  
Is 1.64.0 not supported any more?

---

## Comment 1

> Username: mjcaisse  
> Created at: 2019-06-26 15:43:26 UTC  
> Url: https://github.com/boostorg/boost/issues/236#issuecomment-505931970  

Hi @zoeeer -  
  
You can perform the following to get the 1.64.0 release by tag:  
  
```  
git clone git@github.com:boostorg/boost.git boost-1_64_0  
cd boost-1_64_0  
git fetch --tags  
git checkout boost-1.64.0  
git submodule update --init --recursive  
```  
You can also use `--depth 1` to fetch just what you need:  
  
```git submodule update --depth 1 --init --recursive```
