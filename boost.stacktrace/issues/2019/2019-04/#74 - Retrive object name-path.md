# #74 - [Feature Request] Retrive object name/path [Closed]

> Username: Chocobo1  
> Created at: 2019-04-15 10:53:27 UTC  
> Updated at: 2019-05-04 05:00:36 UTC  
> Closed at: 2019-05-03 18:39:35 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/74  

First of all, thank you for this library.  
  
I'm missing a feature that I think is very useful for debugging, that is accessing the object name/path where the function resides (not sure if there is a name for it).  
For example, I wish to get the name `./prog` from the example in http://man7.org/linux/man-pages/man3/backtrace.3.html#EXAMPLE  
Also another example that is used in action (the path in front of the line):  
```  
/usr/lib/x86_64-linux-gnu/libQt5Core.so.5 : QThread::exec()+0xb6 [0x7f1228d52ec6]  
```  
  
Pardon me if this feature is already available because I didn't find any info.  
Thank you for your consideration.

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-03 18:39:35 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/74#issuecomment-489198425  

This is available in Boost.DLL library via [symbol_location_ptr](https://www.boost.org/doc/libs/1_70_0/doc/html/boost/dll/symbol_location_ptr.html). Just puth the result of calling `frame::address()` into the `symbol_location_ptr` and you will get the path to the binary with the symbol/frame.

---

## Comment 2

> Username: apolukhin  
> Created at: 2019-05-03 18:40:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/74#issuecomment-489198608  

Thanks for the feature request and interest in the library!

---

## Comment 3

> Username: Chocobo1  
> Created at: 2019-05-04 05:00:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/74#issuecomment-489294345  

Thanks for the help!
