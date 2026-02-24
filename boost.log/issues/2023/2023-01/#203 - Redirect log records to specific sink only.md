# #203 - Redirect log records to specific sink only [Closed]

> Username: traceon  
> Created at: 2023-01-18 11:05:53 UTC  
> Updated at: 2023-01-18 12:36:25 UTC  
> Closed at: 2023-01-18 11:27:22 UTC  
> Url: https://github.com/boostorg/log/issues/203  

I know this could be done using channels and filtering, but while that allows to restrict the records that are going to be delivered to a specific "target" sink, it doesn't allow to exclude those records from all other sinks that were added to the core (or will be added later).  
  
Is there a solution for this issue?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-01-18 11:27:22 UTC  
> Url: https://github.com/boostorg/log/issues/203#issuecomment-1386902173  

You have to configure filters on all sinks. On ones the records in given channels should be passed by filters, on others - suppressed.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-01-18 11:30:05 UTC  
> Url: https://github.com/boostorg/log/issues/203#issuecomment-1386905389  

Or rather, the filters should be blocking records for a given channel that you don't want to pass. By default, if you don't check the channel and the record is not suppressed based on other attributes (or if you don't have a filter a all) then the record will pass to the sink.

---

## Comment 3

> Username: traceon  
> Created at: 2023-01-18 11:46:43 UTC  
> Url: https://github.com/boostorg/log/issues/203#issuecomment-1386924651  

Right, and this is not possible to achieve, when the different parts of the program are using Boost.Log, and can't really coordinate (independent libs, for example). Neither is it possible to check if there are other sinks installed to kind of fail gracefully, although, this wouldn't protect for future misconfigured sinks.

---

## Comment 4

> Username: Lastique  
> Created at: 2023-01-18 12:35:23 UTC  
> Updated at: 2023-01-18 12:36:25 UTC  
> Url: https://github.com/boostorg/log/issues/203#issuecomment-1386984086  

1. Libraries should document the attributes they use so that the users of the library are able configure logging. I.e. attributes are part of the library API.  
2. Libraries should not be setting up sinks or filters. This is the library user's job (i.e. the application should be the only place where logging is configured).
