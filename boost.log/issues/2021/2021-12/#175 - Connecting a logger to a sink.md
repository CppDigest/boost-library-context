# #175 - Connecting a logger to a sink [Closed]

> Username: ropieur  
> Created at: 2021-12-16 14:52:21 UTC  
> Updated at: 2021-12-18 12:01:03 UTC  
> Closed at: 2021-12-16 17:49:47 UTC  
> Url: https://github.com/boostorg/log/issues/175  

Hi,  
I read on the boost users archives that it is not possible to [connect a logger to a specific sink](https://lists.boost.org/boost-users//2013/12/80696.php) directly and we should go with attributes and filtering. IMHO, I think this introduces a performance penalty compared to a direct link, due to the evaluation of the additional filtering. What do you suggest to minimize this impact of filtering? Adopt an integer attribute?  
Thank you

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-16 17:49:47 UTC  
> Url: https://github.com/boostorg/log/issues/175#issuecomment-996043447  

> IMHO, I think this introduces a performance penalty compared to a direct link...  
  
Filtering, as well as any other flexibility has a cost, and the question is whether you need that kind of flexibility. For example, the simplest way to implement a logging system that writes directly to a file is... to use `std::ofstream`. Boost.Log aims to offer a more flexible solution, so yes, filtering is the way to pass log records to select sinks and not the others.  
  
Boost.Log supports [channels](https://www.boost.org/doc/libs/1_78_0/libs/log/doc/html/log/detailed/sources.html#log.detailed.sources.channel_logger), which can be used to limit where log records end up being processed. As to what type to use in attribute values, obviously, an integer would be faster than something more complex, but you will need to profile your application to see whether that will actually matter.

---

## Comment 2

> Username: ropieur  
> Created at: 2021-12-18 09:57:08 UTC  
> Url: https://github.com/boostorg/log/issues/175#issuecomment-997178937  

> Filtering, as well as any other flexibility has a cost, and the question is whether you need that kind of flexibility.  
For example, the simplest way to implement a logging system that writes directly to a file is... to use `std::ofstream`. Boost.Log aims to offer a more flexible solution, so yes, filtering is the way to pass log records to select sinks and not the others.  
  
In some area of the application, I really need the flexibility provided by boost log, which is the most powerful I saw amongst all available logging libraries. Just for that, really big thanks for such a job. The point is that some parts of the application must log in their own files, where I need the possibility to control the severity only and the rotation customization. Actually you could see it as the application would have multiple separated cores instead of just a global one. That means that we can control where the records are spreaded. Also why to apply a filter (which has a runtime cost) on records that I already know they never reach some sinks?  
  
> Boost.Log supports [channels](https://www.boost.org/doc/libs/1_78_0/libs/log/doc/html/log/detailed/sources.html#log.detailed.sources.channel_logger), which can be used to limit where log records end up being processed. As to what type to use in attribute values, obviously, an integer would be faster than something more complex, but you will need to profile your application to see whether that will actually matter.  
  
Thank you, I will explore this solution

---

## Comment 3

> Username: Lastique  
> Created at: 2021-12-18 12:01:03 UTC  
> Url: https://github.com/boostorg/log/issues/175#issuecomment-997192609  

> Also why to apply a filter (which has a runtime cost) on records that I already know they never reach some sinks?  
  
Filtering *is* the way to know whether a given record is supposed to reach a given sink. You may want to rearrange the log records between files at some point, and filtering allows to do this with no change to the logging code. If you are absolutely sure you won't need this and the performance cost is critical, use a different solution for those parts of your code.  
  
The design choice to have a single global logging core is deliberate. Logging library configuration and use would be much more complicated otherwise. See https://github.com/boostorg/log/issues/88#issuecomment-505961785.
