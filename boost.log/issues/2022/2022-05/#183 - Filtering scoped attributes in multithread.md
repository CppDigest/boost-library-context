# #183 - Filtering scoped attributes in multithread [Closed]

> Username: ropieur  
> Created at: 2022-05-12 10:59:17 UTC  
> Updated at: 2022-05-12 12:37:29 UTC  
> Closed at: 2022-05-12 11:58:36 UTC  
> Url: https://github.com/boostorg/log/issues/183  

Hi,  
I implemented a custom attribute, let's say `ID`, for which I provided a custom filtering relation called `in`, capable to check whether the `ID` belongs to a `std::vector<ID>`, so that I can setup a filter like `%ID% in { 1, 2, 3 }`  
  
The filtering works perfectly well (verified by cout output during his evaluation)  
  
A class Entity has a global severity_logger_mt, which can be accessed by multiple threads  
  
Entity.cpp  
```  
logging::sources::severity_logger_mt<Severity> & logger()  
{  
    static logging::sources::severity_logger_mt<Severity> l;  
    return l;  
}  
  
void Entity::f(ID id)  
{  
    BOOST_LOG_SCOPED_LOGGER_ATTR(logger(), "ID", logging::attributes::constant(id));  
    BOOST_LOG_SEV(logger(), notice) << "f called";  
    ...  
}  
```  
  
I observe that some records not belonging to the specified filtering list are still displayed.  
I am wondering if I can use scoped attributes on a single global logger_mt from multiple threads or if this usage could be the root cause of my issue observations.  
  
Any help/suggestion would be very welcome.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-12 11:58:36 UTC  
> Url: https://github.com/boostorg/log/issues/183#issuecomment-1124902703  

Using scoped attributes with a global logger and multiple threads will not work correctly. It is possible that multiple threads try to modify or use the ID attribute (either add or remove it) in the shared logger concurrently. For example, thread A may add the attribute to the logger, then the other thread B may emit an unrelated log record through this logger; that log record will have ID attached. Or, if both threads try to add an ID attribute, only one thread will succeed and the other thread will emit log records with the ID value registered by the first thread.  This may lead to either some log records having the attribute value attached while they shouldn't or vise versa. Consequently, you may see unintended log records or not see the intended ones in the filtered output.  
  
You need to either use separate loggers for different threads (or objects they relate to) or use [thread-specific](https://www.boost.org/doc/libs/1_79_0/libs/log/doc/html/log/detailed/attributes.html#log.detailed.attributes.related_components.scoped_attributes) scoped attributes. Basically, you need to ensure that threads don't interfere over the attributes they add or remove.

---

## Comment 2

> Username: ropieur  
> Created at: 2022-05-12 12:26:34 UTC  
> Url: https://github.com/boostorg/log/issues/183#issuecomment-1124929979  

Hi @Lastique ,  
thank you for your swift reply.  
Would you mean that the following would solve my issue? (note the addition of `thread_local` keyword)  
  
```  
logging::sources::severity_logger_mt<Severity> & logger()  
{  
    static thread_local logging::sources::severity_logger_mt<Severity> l;  
    return l;  
}  
```

---

## Comment 3

> Username: Lastique  
> Created at: 2022-05-12 12:31:06 UTC  
> Updated at: 2022-05-12 12:32:57 UTC  
> Url: https://github.com/boostorg/log/issues/183#issuecomment-1124934548  

Yes, that's one way to do this. At this point you probably don't need the logger to be `_mt`, unless you pass a reference to it between threads. In which case you would have the same problem again.

---

## Comment 4

> Username: ropieur  
> Created at: 2022-05-12 12:37:29 UTC  
> Url: https://github.com/boostorg/log/issues/183#issuecomment-1124941183  

Oh yes, obviously! Thank you for the observation
