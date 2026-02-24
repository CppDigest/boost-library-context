# #9 - Integration/optional header problem [Open]

> Username: Kojoley  
> Created at: 2018-12-30 00:22:30 UTC  
> Updated at: 2019-03-04 22:24:13 UTC  
> Url: https://github.com/boostorg/boostdep/issues/9  

Currently `boostdep` will report as a dependency every include of every header it finds within an include folder. It would be nice if there was a way to tell `boostdep` that the header is optional, lets say with something like a comment line that contains `boostdep:integration_header`. To sanitize misuse of such headers they must not be included from other headers within a library that are not marked as well optional. When integration header is used from other libraries it is considered a dependency though, and the mark should be ignored.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-12-30 00:34:31 UTC  
> Updated at: 2018-12-30 19:30:06 UTC  
> Url: https://github.com/boostorg/boostdep/issues/9#issuecomment-450530916  

For example dependency on Serialization library because of such headers:  
### DateTime  
https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/posix_time/time_serialize.hpp  
https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/gregorian/greg_serialize.hpp  
### DynamicBitset  
https://github.com/boostorg/dynamic_bitset/blob/develop/include/boost/dynamic_bitset/serialization.hpp  
### Flyweight  
https://github.com/boostorg/flyweight/blob/develop/include/boost/flyweight/serialize.hpp  
https://github.com/boostorg/flyweight/blob/develop/include/boost/flyweight/detail/archive_constructed.hpp  
https://github.com/boostorg/flyweight/blob/develop/include/boost/flyweight/detail/serialization_helper.hpp  
### Units  
https://github.com/boostorg/units/blob/develop/include/boost/units/io.hpp  
### UUID  
https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/uuid_serialize.hpp

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2019-03-04 12:24:30 UTC  
> Url: https://github.com/boostorg/boostdep/issues/9#issuecomment-469234061  

One idea would be to treat them as part of a separate library (e.g. DateTime~Serialization). Maybe I can prototype this in boost_dep_graph.

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2019-03-04 22:20:13 UTC  
> Updated at: 2019-03-04 22:24:13 UTC  
> Url: https://github.com/boostorg/boostdep/issues/9#issuecomment-469445260  

It would certainly get rid of the biggest dependency cycle:   
![boostdep-fine2](https://user-images.githubusercontent.com/43199315/53766933-9bd61c00-3ed4-11e9-9430-27e13833f962.png)
