# #2 - `b2 install python=2.7 python=3.3` fails [Closed]

> Username: pdimov  
> Created at: 2019-04-14 23:52:51 UTC  
> Updated at: 2019-04-17 21:11:46 UTC  
> Closed at: 2019-04-17 21:11:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/2  

... with  
  
```  
error: Name clash for '<pc:\boost-prefix\lib\cmake\boost_python-1.71.0>libboost_python-variant-shared.cmake'  
error:  
error: Tried to build the target twice, with property sets having  
error: these incompatible properties:  
error:  
error:     -  <python>2.7  
error:     -  <python>3.3  
error:  
error: Please make sure to have consistent requirements for these  
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
See https://github.com/boostorg/python/issues/262.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-15 00:50:03 UTC  
> Url: https://github.com/boostorg/boost_install/issues/2#issuecomment-483074404  

@swatanabe Can you please offer some advice on this one? I hardcode boostcpp.tag here: https://github.com/boostorg/boost_install/blob/develop/boost-install.jam#L314, but the `boost_python` and `boost_numpy` libraries use their own local `python-tag`, which takes the value of `<python>` into account. I could of course special-case these, but it might be more proper to somehow use the `<tag>` of the library, instead of hardcoding `boostcpp.tag`.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-17 21:11:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/2#issuecomment-484263260  

Should be fixed in `develop`.
