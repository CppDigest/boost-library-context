# #213 - Formatting TimeStamp [Closed]

> Username: lijh8  
> Created at: 2023-07-02 06:36:24 UTC  
> Updated at: 2023-07-02 09:04:57 UTC  
> Closed at: 2023-07-02 09:04:56 UTC  
> Url: https://github.com/boostorg/log/issues/213  

```  
    auto format = expr::stream  
        << expr::attr<boost::posix_time::ptime>("TimeStamp")  
        << " [" << std::left << std::setw(7) << std::setfill(' ') << logging::trivial::severity << "] "  
        << expr::smessage;  
  
    sink->set_formatter(format);  
```  
    // 2023-Jul-02 14:08:54.877281   
  
The code generates data time with month part in abbr letters.  
  
I added two more lines to get data time in all digit format.  
It allocates memory manually but asan does not warn on it.   
  
```  
    auto *facet = new boost::posix_time::time_facet("%Y%m%d %H%M%S");  
    std::locale::global(std::locale(std::locale(), facet));  
```  
  
    // 2023-07-02 14:08:54  
  
Is there a better way to do it?  
  
Thanks

---

## Comment 1

> Username: Lastique  
> Created at: 2023-07-02 09:04:56 UTC  
> Url: https://github.com/boostorg/log/issues/213#issuecomment-1616494331  

Depends on what you actually need. Here are a couple alternatives:  
  
1. Setting the global locale is fine, but in case if you want to only set the locale for a given sink, you can call [`imbue`](https://www.boost.org/doc/libs/1_82_0/libs/log/doc/html/boost/log/sinks/basic_formatting__idm32293.html#idm32362-bb) on the sink frontend.  
2. You can avoid creating the locale altogether by using a dedicated [formatter for dates and time](https://www.boost.org/doc/libs/1_82_0/libs/log/doc/html/log/detailed/expressions.html#log.detailed.expressions.formatters.date_time). It will work slightly more efficiently than the locale.
