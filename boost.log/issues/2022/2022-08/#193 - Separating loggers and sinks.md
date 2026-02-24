# #193 - Separating loggers and sinks [Closed]

> Username: fsimonis  
> Created at: 2022-08-22 15:07:07 UTC  
> Updated at: 2022-08-30 07:03:52 UTC  
> Closed at: 2022-08-28 21:24:34 UTC  
> Url: https://github.com/boostorg/log/issues/193  

### Problem summary  
I am using boost log in a library. One application using that library also uses boost log.  
  
The result are duplicate log entries in the sinks as both the library and the application add their own logger and sink(s).  
  
How do I separate these logs entries?  
  
Schematically, I am looking for this:  
  
![image](https://user-images.githubusercontent.com/13552216/185953894-b8c2a968-a356-4129-8f68-f54f1129e753.png)  
  
### What I tried  
  
Defining a boolean constant attribute `IsLibrary = true` in the library logger allows to remove non-library entries using a filter in the library sinks. The application sinks, still print entries from all sources.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-08-22 16:23:04 UTC  
> Updated at: 2022-08-22 17:00:05 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1222593432  

Libraries should not be adding any sinks, only the application should. Libraries produce log records, but it is application's job to decide what to do with them. It is a good idea to mark log records produced by the library with an attribute (e.g. [channel](https://www.boost.org/doc/libs/1_80_0/libs/log/doc/html/log/detailed/sources.html#log.detailed.sources.channel_logger)) so that the application is able to use that information in filters to separate log records produced by different libraries and the application itself.

---

## Comment 2

> Username: fsimonis  
> Created at: 2022-08-23 07:05:29 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1223641346  

> Libraries should not be adding any sinks, only the application should. Libraries produce log records, but it is application's job to decide what to do with them.  
  
This assumes that the application is written in C++, is using boost log and that the user can modify its logging code.  
  
Even if the above is true, then the library logs still need to be formatted differently than the application logs as the library provides additional attributes. This requires separate sinks for both the application and the library, which need to filter on the channel (or some other attribute), essentially leading back to the original problem.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-08-23 09:51:49 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1223833902  

> This assumes that the application is written in C++, is using boost log and that the user can modify its logging code.  
  
Yes, that's the design.  
  
Alternatively, you can provide API in your library to configure logging on application's behalf. But ultimately there should be a single point of decision as to how to configure logging, and logically that should be the application, not the library.  
  
> Even if the above is true, then the library logs still need to be formatted differently than the application logs as the library provides additional attributes.  
  
The library must document its log attributes. As to how log records are formatted, again, that should be part of logging configuration and therefore application's choice. There might not be any formatting at all if the application chooses a sink that does not use formatting. This should not affect the library in any way.  
  
> This requires separate sinks for both the application and the library, which need to filter on the channel (or some other attribute), essentially leading back to the original problem.  
  
Filtering by channel does not require a separate sink, it all depends on what you want to achieve. If you want logs from all libraries and application in one file, have a single sink and configure its filter and formatter appropriately. You may have to write the filter and formatter functions manually if log records from different sources are very different or you want to format them differently. But often that is not needed as the set of attributes is pretty standard most of the time.

---

## Comment 4

> Username: fsimonis  
> Created at: 2022-08-23 10:57:31 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1223902053  

> Yes, that's the design.  
>   
> Alternatively, you can provide API in your library to configure logging on application's behalf. But ultimately there should be a single point of decision as to how to configure logging, and logically that should be the application, not the library.  
  
I see. It would be helpful to have a dedicated section/page/FAQ about this in the documentation. `Using boost.log in libraries.`  
  
> The library must document its log attributes. As to how log records are formatted, again, that should be part of logging configuration and therefore application's choice. There might not be any formatting at all if the application chooses a sink that does not use formatting. This should not affect the library in any way.  
  
My point here was that some log entries need additional context, which may require different formatting.  
  
> Filtering by channel does not require a separate sink, it all depends on what you want to achieve.  
  
I basically require different filters for different channels.  
Consider the following toy-example:  
  
Channel | Attributes | Format  
--- | --- | ---   
Application | Runtime | `%Runtime%:%Channel% %Message`  
Library | TimeStamp, Module | `%TimeStamp%:%Channel%:%Module% %Message%`  
  
Is there a way to conditionally pick formatters based on some criterion?  
Something like this:  
```cpp  
sink->set_formatter(  
  conditional("%Channel% == Application", applicationFormatter, libraryFormatter)  
)  
```

---

## Comment 5

> Username: Lastique  
> Created at: 2022-08-23 13:01:42 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1224042971  

> Is there a way to conditionally pick formatters based on some criterion?  
  
Yes, see [here](https://www.boost.org/doc/libs/1_80_0/libs/log/doc/html/log/detailed/expressions.html#log.detailed.expressions.formatters.conditional). You can also write [your own formatting function](https://www.boost.org/doc/libs/1_80_0/libs/log/doc/html/log/tutorial/formatters.html#log.tutorial.formatters.custom_formatting_functions) where you can implement any behavior you need.  
  
For filters, you may be interested in [this](https://www.boost.org/doc/libs/1_80_0/libs/log/doc/html/log/detailed/expressions.html#log.detailed.expressions.predicates.channel_severity_filter).

---

## Comment 6

> Username: fsimonis  
> Created at: 2022-08-24 13:46:27 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1225750471  

Thank you very much for the help!  
  
To summarize:  
  
A library using boost.log should  
* document its generated attributes  
* make its log entries detectable using a `channel_logger` or a tag such as a `constant<bool>("LibraryName", true)`  
* prevent to mess with `boost::core` as much as possible  
* provide an option to disable library-provided sinks, if the library plans to provide sinks as a fallback.  
  
The application:  
  
* should partition the log entries on the aforementioned tag to achieve separate sinks for application and library logs.  
* can use conditional formatters on the aforementioned tag to achieve separate formatting for application and library logs.  
* should tag and filter its own log entries to use the library fallback sink.

---

## Comment 7

> Username: fsimonis  
> Created at: 2022-08-24 13:47:30 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1225751917  

Some visible guidelines for library developers in the docs would be very helpful though. :wink:

---

## Comment 8

> Username: fsimonis  
> Created at: 2022-08-30 07:03:52 UTC  
> Url: https://github.com/boostorg/log/issues/193#issuecomment-1231235609  

Thanks for doc! Looks very useful!
