# #135 - Deadlock in asynchronous_sink::stop() [Closed]

> Username: rcane  
> Created at: 2020-12-01 01:02:02 UTC  
> Updated at: 2020-12-01 12:37:36 UTC  
> Closed at: 2020-12-01 12:37:22 UTC  
> Url: https://github.com/boostorg/log/issues/135  

The commit e0cb0b7 fixing #131 seems to have introduced a deadlock in the ```stop()``` method. The previous version of the code explicitly called ```queue_base_type::interrupt_dequeue()``` which stopped the blocking wait in ```run()```. Now this call is no longer made making ```stop()``` wait forever for ```join()``` to complete.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-12-01 12:37:36 UTC  
> Url: https://github.com/boostorg/log/issues/135#issuecomment-736525598  

Thanks for the report.
