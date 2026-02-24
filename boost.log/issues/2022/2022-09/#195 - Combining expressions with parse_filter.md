# #195 - Combining expressions with parse_filter [Closed]

> Username: fsimonis  
> Created at: 2022-09-06 13:39:38 UTC  
> Updated at: 2025-07-08 21:09:10 UTC  
> Closed at: 2025-07-08 21:08:17 UTC  
> Url: https://github.com/boostorg/log/issues/195  

I am working with a filter that consists of two parts.  
  
The first part is known and can be written using the expression language.  
The second part is a user-provided string, so I need to use `parse_filter`.  
  
Is it possible to combine an expression with the result of `parse_filter`?

---

## Comment 1

> Username: Lastique  
> Created at: 2025-07-08 21:08:17 UTC  
> Updated at: 2025-07-08 21:09:10 UTC  
> Url: https://github.com/boostorg/log/issues/195#issuecomment-3050305437  

Yes. A filter returned by `parse_filter` is a function object that can be injected into the filter expression like this:  
  
```  
#include <boost/log/trivial.hpp>  
#include <boost/log/expressions/attr.hpp>  
#include <boost/log/expressions/filter.hpp>  
#include <boost/log/utility/setup/filter_parser.hpp>  
#include <boost/phoenix/bind.hpp>  
#include <boost/phoenix/operator.hpp>  
#include <boost/phoenix/core/argument.hpp>  
  
int main()  
{  
    boost::log::filter parsed_filter =  
        boost::log::parse_filter("%Channel% = \"MyChannel\"");  
    boost::log::filter full_filter =  
        boost::log::expressions::attr< boost::log::trivial::severity_level >("Severity") >= boost::log::trivial::severity_level::info &&  
        boost::phoenix::bind(parsed_filter, boost::phoenix::placeholders::_1);  
}  
```  
  
[Godbolt](https://godbolt.org/z/98hTr5sEE)  
  
Here, `phoenix::bind` wraps the parsed formatter function object so that it can participate in the filter expression. When invoked, it just forwards the call to the wrapped filter. I should probably add a simpler wrapper that does that without having to resort to `phoenix::bind`.  
  
PS: Sorry for the late response. I must have missed this issue somehow.
