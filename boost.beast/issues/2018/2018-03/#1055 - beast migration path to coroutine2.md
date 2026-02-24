# #1055 - beast migration path to coroutine2? [Closed]

> Username: jdmairs  
> Created at: 2018-03-02 18:10:25 UTC  
> Updated at: 2018-03-02 18:54:05 UTC  
> Closed at: 2018-03-02 18:44:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1055  

I shamefully admit part of my reason for posting here is how quickly Vinnie responds to issues.  This is most likely not a Beast problem but I'm hoping to tap his knowledge on co-routines.    
  
I built a REST based server built upon the http_client_coro.cpp example which uses stackful coroutines.  During one of our request handlers the app needs to reread an xml config file using boost::property_tree.  In the read_xml_internal method we get a Access Violation exception.  The top of the stack shows the function _chkstk() being called.  Using VS2017 core guideline checker we get C6262 warning in the boost property tree that too much stack space is being used.  We set the Linker->System->Stack Reserve Size to 4194304 but the crash still happens.    
  
This call to reload the property tree does not fail if we call it outside of the lambda that gets invoked by the REST server.    
  
No large arrays are being used.    
  
Would coro2 or stackless coroutines be a solution?  
  
Any thoughts?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-02 18:30:27 UTC  
> Updated at: 2018-03-02 18:31:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1055#issuecomment-370010769  

Always glad to help when I can.  
  
> We set the Linker->System->Stack Reserve Size to 4194304 but the crash still happens.  
  
LOL...sorry for having a little chuckle at your expense. This setting only affects the stacks created by the compiler. It can't affect the stacks created by the coroutine library. When you spawn the coroutine you can adjust the stack size by passing in some additional settings. For example, this spawns a coroutine with 2MB of stack space:  
  
```  
boost::asio::spawn(ioc, f, boost::coroutines::attributes(2 * 1024 * 1024));  
```  
  
Or you can adjust the settings used by Boost.PropertyTree, as illustrated in the answer here:  
  
https://stackoverflow.com/questions/41030285/boostproperty-treeread-xml-segfaults-in-an-asio-handler-spawned-using-boost  
  
Hope this helps!

---

## Comment 2

> Username: jdmairs  
> Created at: 2018-03-02 18:44:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1055#issuecomment-370015033  

Wow how cool that we can fix it either via coroutine or ptree.  The power/design of boost continues to amaze.

---

## Comment 3

> Username: jdmairs  
> Created at: 2018-03-02 18:54:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1055#issuecomment-370017646  

Thanks so much again!
