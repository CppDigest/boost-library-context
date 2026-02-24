# #524 - Build warning seen while building boost/status [Closed]

> Username: jeking3  
> Created at: 2018-10-28 01:02:39 UTC  
> Updated at: 2018-11-26 18:23:27 UTC  
> Closed at: 2018-11-26 18:23:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/524  

toolset=clang-6 cxxstd=03 variant=release  
```  
../boost/geometry/srs/projections/proj/isea.hpp:1070:25: warning: enumeration values 'isea_addr_geo', 'isea_addr_interleave', and 'isea_addr_plane' not handled in switch [-Wswitch]  
                switch (g->output) {  
                        ^  
1 warning generated.  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-11-10 12:38:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/524#issuecomment-437581034  

Thanks, I'll try to push a fix to 1.69.

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-11-26 18:23:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/524#issuecomment-441744198  

Done
