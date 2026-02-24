# #136 - Convenient way to add datetime, source location, log level in log message [Closed]

> Username: lijh8  
> Created at: 2020-12-16 15:41:37 UTC  
> Updated at: 2020-12-16 19:50:55 UTC  
> Closed at: 2020-12-16 19:50:55 UTC  
> Url: https://github.com/boostorg/log/issues/136  

Can we as convenient as google glog to add datetime, source location, log level in log message?  
  
I need to change a lot places in boost log to achieve this. I do not want to add another library for just doing logging functionality. Boost is already a huge library.  
  
```  
// glog  
LOG(INFO) << "some info";  
LOG(ERROR) << "some error";  
```  
  
```  
//output  
//level, datetime, source location, …  
I0327 11:17:12.96346  435 test.cc:9] some info  
E0327 11:17:12.96404  435 test.cc:11] some error  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2020-12-16 19:50:55 UTC  
> Url: https://github.com/boostorg/log/issues/136#issuecomment-746911956  

The simplest way to use Boost.Log is to use [trivial logging](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/tutorial.html#log.tutorial.trivial), which already provides timestamps and severity level in the output.  
  
Timestamp and severity level and more can be added as attributes, see [here](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/tutorial/attributes.html) for an example. `add_common_attributes` is also documented [here](https://www.boost.org/doc/libs/1_75_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.setup.convenience). This allows for customizing the types of these attributes, as well as filtering and formatting depending on the types.  
  
Source locations of log record statements can only be added as a user-defined macro, and thus is not provided by the library. [Here](https://stackoverflow.com/questions/35895199/boost-log-and-severity-local-attributes/35919119#35919119) is an example, along with some other options, with their pros and cons.
