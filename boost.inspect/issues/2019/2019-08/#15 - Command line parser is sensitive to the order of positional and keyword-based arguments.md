# #15 - Command line parser is sensitive to the order of positional and keyword-based arguments [Open]

> Username: HDembinski  
> Created at: 2019-08-22 09:18:43 UTC  
> Updated at: 2019-08-22 09:18:43 UTC  
> Url: https://github.com/boostorg/inspect/issues/15  

The inspect tool requires me to put the positional arguments first and then the `-options`. This is not up to modern standards, where command line parsers generally allow positional arguments and keyword-based options to be interleaved in any order.
