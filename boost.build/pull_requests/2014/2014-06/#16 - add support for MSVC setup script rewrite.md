# #16 add support for MSVC setup script rewrite [Merged]

> Username: jivancic  
> Created at: 2014-06-27 08:26:12 UTC  
> Updated at: 2021-10-02 22:35:57 UTC  
> Merged at: 2014-07-02 07:13:49 UTC  
> Closed at: 2014-07-02 07:13:49 UTC  
> Url: https://github.com/boostorg/build/pull/16  

MSVC setup scripts are very slow, so should not be used in build actions directly.  
Rewrite these scripts into their fast equivalents and use those instead.  
Add toolset initialization option <rewrite-setup-script> to customize/disable this new behavior.

---

## Comment 1

> Username: jhunold  
> Created_at: 2014-07-01 07:02:51 UTC  
> Url: https://github.com/boostorg/build/pull/16#issuecomment-47623354  

The declaration of "rewrite-setup-scripts" is missing.   
  
 b2 -j10 rewrite-setup-scripts=always  
  
 BOOST_ROOT/tools/build/src/build\feature.jam:325: in validate-feature from module feature  
 error: unknown feature "<rewrite-setup-scripts>"

---

## Comment 2

> Username: jivancic  
> Created_at: 2014-07-01 08:13:21 UTC  
> Url: https://github.com/boostorg/build/pull/16#issuecomment-47628732  

It is not intended to be a feature. It is a toolset setup option, like `<compiler>`, `<setup>` etc. i.e. is used like this:  
  
```  
using msvc : 12.0 : : <rewrite-setup-scripts>always ;  
```  
  
I don't think declaring it as a feature has much sense here - feature values are applied to targets. Toolset initialization does not have a property set to query. The only way to achieve the above is to manually check the command line (i.e. add another `--` option, instead of a `feature=value`) when deciding whether to generate scripts.

---
