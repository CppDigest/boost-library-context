# #115 - Reported number of non-native objects sent always 1 [Open]

> Username: psbritt  
> Created at: 2020-04-01 17:06:30 UTC  
> Updated at: 2020-06-22 16:04:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/115  

Line 182 of details/request_handlers.hpp, the boost:status object has the count hard coded to 1. The deserialize method at line 260 takes no boost::status object. Legacy deserialize method takes a boost::status object and keeps track of number of objects that have been deserialized and sets count to that number.

---

## Comment 1

> Username: aminiussi  
> Created at: 2020-06-22 16:04:15 UTC  
> Url: https://github.com/boostorg/mpi/issues/115#issuecomment-647617395  

Hi,   
  
Do you have an actual example ? I'm not sure I see a case where it should reply anything else than 1.  
  
Thanks
