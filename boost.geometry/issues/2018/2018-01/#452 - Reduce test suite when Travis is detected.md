# #452 - Reduce test suite when Travis is detected? [Closed]

> Username: pdimov  
> Created at: 2018-01-16 14:35:18 UTC  
> Updated at: 2018-01-22 22:15:13 UTC  
> Closed at: 2018-01-20 22:38:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/452  

Would it be possible to run a reduced version of the test suite when Travis is detected? Perhaps something along the lines of  
  
```  
import os ;  
  
build-project core ;  
// other critical tests  
  
if ! [ os.environ TRAVIS ]  
{  
  
build-project concepts ;   
// other less critical tests  
  
}  
```  
  
The reason Travis matters, even though you don't use it at the moment, is that the superproject Travis runs the library tests when a library is updated, such as f.ex. here: https://travis-ci.org/boostorg/boost/builds/329477374

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-01-16 14:51:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/452#issuecomment-357984313  

Right, running all of the tests take very long time. I'll implement some quick, general test and enable it for travis.  
  
Btw, do I recall correctly that some mechanism was added to Boost's testing framework for this purpose? I.e. to distinguish between critical and extensive testing. Because if that was the case then I could also add support for it.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-01-16 15:01:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/452#issuecomment-357987173  

The documentation currently says that you can define `minimal`, `full` and `extra` test suites, but nothing uses them at the moment. They were part of Rene's future plans, but he quit, so I doubt that anything will come of it.

---

## Comment 3

> Username: awulkiew  
> Created at: 2018-01-16 15:15:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/452#issuecomment-357991770  

Thanks. I'll make the change before the end of the week.

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-01-20 22:38:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/452#issuecomment-359207661  

It seems to work as expected (https://travis-ci.org/boostorg/boost/builds/331059922), closing.
