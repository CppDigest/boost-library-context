# #202 - Performance issue: Boost.Log does a huge work when filtering records [Open]

> Username: evgn  
> Created at: 2023-01-10 16:22:57 UTC  
> Updated at: 2023-01-14 11:55:41 UTC  
> Url: https://github.com/boostorg/log/issues/202  

I worked with Boost.Log library and encountered a huge performance impact in case when all content passed to the logger supposed to be filtered.  
I reduced the logger to the simplest one with a console backend and the result remained the same.  
  
Here is the sample code:  
```  
#include <boost/log/sources/severity_feature.hpp>  
#include <boost/log/sources/severity_logger.hpp>  
#include <boost/log/common.hpp>  
#include <boost/log/utility/setup/console.hpp>  
#include <boost/log/expressions.hpp>  
#include <boost/log/utility/setup/file.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/support/date_time.hpp>  
#include <boost/phoenix/bind.hpp>  
  
enum severity_level  
{  
    critical,  
    info,  
    trace  
};  
  
BOOST_LOG_ATTRIBUTE_KEYWORD(severity_level_kw, "Severity", severity_level)  
  
using backend_type = boost::log::sinks::synchronous_sink<boost::log::sinks::basic_text_ostream_backend<char>>;  
  
bool filter_impl(severity_level lvl) { return lvl < trace; }  
bool filter(const boost::log::value_ref<severity_level, tag::severity_level_kw> & lvl) {  
    return filter_impl(*lvl);  
}  
  
void logging_function() {  
    auto sink = boost::make_shared<backend_type>();  
    sink->set_filter(boost::phoenix::bind(&filter, severity_level_kw.or_none()));  
    sink->locked_backend()->add_stream(boost::shared_ptr<std::ostream>(&std::cout, boost::null_deleter{}));  
    boost::log::core::get()->add_sink(std::move(sink));  
  
    boost::log::sources::severity_logger<severity_level> slg;  
    for(int i = 0; i < 100000000; ++i) {  
        // I expect this line only applies the filter and does nothing else since trace-messages supposed to be filtered  
        BOOST_LOG_SEV(slg, trace) << "Trace message: " << i;  
    }  
}  
  
int main()  
{  
    logging_function();  
    return 0;  
}  
```  
This simple program works for ~14 seconds.  
But if I change the loop like this:  
```  
for(int i = 0; i < 100000000; ++i) {  
    if(filter_impl(trace))  
        std::cout << "Debug message: " << i << "\n";  
}  
```  
the program completes instantly as it supposed to. And it's what I expect the log library should do.  
  
I profiled the program with dotTrace and here is the result  
  
![image](https://user-images.githubusercontent.com/4507636/211599164-acf0b592-d8da-410a-b6c2-2bcd41ffebe7.png)  
  
As you can see `filter` function takes only 95ms and the rest time goes to another stuff including memory allocations which I wouldn't expect to see.  
  
This problem makes Boost.Log unusable in cases when performance is a priority.  
  
As a hot-fix one can filter records manually by introducing a special macro, for example.  
  
But is it worth expecting this issue will be fixed?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-01-10 17:17:46 UTC  
> Url: https://github.com/boostorg/log/issues/202#issuecomment-1377594536  

The library will have overhead compared to the raw `std::ostream`, this is inevitable. Constructing a log record and then extracting attribute values from it in filters and formatters isn't free. Normally, an application would be doing mostly its main job with occasional logging, so the performance overhead of logging is insignificant. If this is not your case and you need logging in the hot path then you probably need a simpler logging solution.  
  
That said, I'm interested in optimizing the library. If you have suggestions to this end, feel free to post a comment or PR. I don't think dynamic memory allocations can be completely eliminated, but may be reduced with pooling. Although if memory allocations are your bottleneck then a better solution would be to use a faster memory allocator library, such as [tcmalloc](https://github.com/gperftools/gperftools/).

---

## Comment 2

> Username: evgn  
> Created at: 2023-01-11 13:27:51 UTC  
> Updated at: 2023-01-11 14:12:28 UTC  
> Url: https://github.com/boostorg/log/issues/202#issuecomment-1378752538  

I have no doubts that Boost.Log is a great library with lots of features. We use only a small subset of them and of cause we realize what we pay for. But when I take the `severity_logger` and supply a simple severity filter I expect that the filter will be applied before all these features. Because I expect not to pay for what I don't use.  
  
Surely I don't fully understand the architecture of the library but the first thing comes to my mind is applying filtres somewhere on an upper layer.   
On the other hand a simple severity filtering could be built into the library itself that would allow not to require a custom filter. It would cover a majority of basic usages when you only need to write formatted records with simple severities.  
  
Suppose the most usages look like this:  
```  
LOGGER(trace) << "Trace message: " << params;  
```  
that is on a call side the severity is usually a compile-time constant. Given the logger itself is a singleton and configured only once it seems that it could take a severity requirement as a template parameter and apply filtering at comple-time. As a result, log writing operation could be omitted by the compiler which would make sense for cases with strong performance requirements.  
  
To sum up, I would say 80% users work with only 20% features. And it would be really cool to have a logger optimized for basic usages.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-01-11 14:41:40 UTC  
> Updated at: 2023-01-11 14:45:31 UTC  
> Url: https://github.com/boostorg/log/issues/202#issuecomment-1378853721  

> I have no doubts that Boost.Log is a great library with lots of features. We use only a small subset of them and of cause we realize what we pay for. But when I take the `severity_logger` and supply a simple severity filter I expect that the filter will be applied before all these features. Because I expect not to pay for what I don't use.  
  
Severity level is an attribute like any other, so in this regard you *are* using attributes. The problem is that attributes, and consequently the log record, is not quite lightweight and requires dynamic memory allocations to construct. Furthermore, the severity level type is erased in the process, so the filter has to extract the value from the record when it needs it. Type erasure is necessary to keep the library API stable across all its users (libraries, the application itself, different parts of the code base, etc.) regardless of the attributes they use. It is probably possible to optimize the cost of this machinery, but it cannot be eliminated completely. I'm not sure how significantly it can be reduced, since the only thing that comes to mind is to pool memory and apply small object optimizations.  
  
> Surely I don't fully understand the architecture of the library but the first thing comes to my mind is applying filtres somewhere on an upper layer.  
  
Adding filtering in the logger is an interesting possibility, but you have to understand it has its own downsides. Obviously, the filter will only be applied to log records made through that logger. If you have multiple loggers, you will have to configure the filter in every one of them. Updating the filter during run time also becomes more difficult.  
  
Also, if you want to optimize away log record construction, the logger-specific filter cannot be a normal filter in the current library's terms, because a filter accepts an attribute value set, which is essentially a log record under construction. Which means this logger-specific filter would have to be fairly specialized (e.g. specifically a severity level threshold rather than an arbitrary function).  
  
> Given the logger itself is a singleton and configured only once it seems that it could take a severity requirement as a template parameter and apply filtering at comple-time.  
  
First, loggers are not singletons. They can be made as such, but from logger's perspective it doesn't matter whether it is a singleton or not. And actually it is preferred to not use logger singletons when possible as it has negative performance implications.  
  
Second, filters are rarely defined at compile time. Yes, there are use cases when one wants to build a binary stripped off logging, completely or in part, but this is not achieved though filtering. In fact, the library leaves the implementation of such use case to the user, as the most efficient way to do this is through preprocessor programming. Normally, filters are configured in run time, based on user's settings.

---

## Comment 4

> Username: evgn  
> Created at: 2023-01-11 21:54:30 UTC  
> Updated at: 2023-01-14 11:55:41 UTC  
> Url: https://github.com/boostorg/log/issues/202#issuecomment-1379534825  

> Severity level is an attribute like any other, so in this regard you are using attributes.  
  
That's why I suggest to think about making an exclusion for severity. Maybe there is a simple way to do it at least optionally for basic usages.  
  
> ...If you have multiple loggers...  
 ...a filter accepts an attribute value set, which is essentially a log record under construction...  
  
I agree that there can be more advanced cases when logging plays a key role. However, our usage is as simple as possible: we have only one logger and the only cause makes us dealing with attributes is a need to filter by severity. And it has always been enough for our needs for years. Probably we chose the wrong library and try shooting a mosquito with a bazooka 😄   
  
> First, loggers are not singletons.  
  
I meant creating global loggers via `BOOST_LOG_INLINE_GLOBAL_LOGGER_DEFAULT` macro. I wanted to say that since the logger is global there is no need to pass a logger object through functions and therefore no need to deal with logger template parameters.   
  
>  Yes, there are use cases when one wants to build a binary stripped off logging, completely or in part, but this is not achieved though filtering.  
  
It's just an idea of optimization for some specific cases when the severity level is known at compile time. But I agree that it could be done on user side as a wrapper around the logger.  
  
BTW, as a hot-fix for now I've added a simple filter by adding a condition like this:  
```  
#define LOGGER(level) \  
    if(filter(severity_level::level)) \  
        BOOST_LOG_SEV(logger::get(), severity_level::level)  
```  
where `filter` compares `level` with a severity configured at run time.
