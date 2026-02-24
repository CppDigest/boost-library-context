# #64 - Boost.Python no longer has separate targets for python 2 / 3 [Closed]

> Username: sdebionne  
> Created at: 2018-07-04 08:28:59 UTC  
> Updated at: 2018-08-01 11:06:00 UTC  
> Closed at: 2018-08-01 11:06:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/64  

The symptoms are:  
  
```  
error: Unable to find file or target named  
error:     '/boost/python//boost_python3'  
error: referred to from project at  
error:     'libs/mpi/build'  
```  
  
And indeed `boost_python3` does not exist anymore in the [Jamfile](https://github.com/boostorg/python/blob/develop/build/Jamfile)  
  
I'll try to do a PR to fix that. I am not sure why this passed the tests though.

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-08-01 11:06:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/64#issuecomment-409538196  

Hi,   
  
I think this was fixed with https://github.com/boostorg/mpi/pull/65  
  
Thanks
