# #178 - parser.hpp requires print_parser declaration for custom parsers [Closed]

> Username: vmauery  
> Created at: 2024-06-10 16:30:55 UTC  
> Updated at: 2024-09-29 20:37:51 UTC  
> Closed at: 2024-09-29 20:37:50 UTC  
> Url: https://github.com/boostorg/parser/issues/178  

When I tried adding a print_parser for my custom parser, ultimately I had to add it before including parser.hpp like this:  
  
```  
namespace boost::parser  
{  
template <typename T>  
struct custom_parser;  
namespace detail  
{  
template <typename Context, typename T>  
void print_parser(Context const& context, custom_parser<T> const&,  
                  std::ostream&, int components = 0);  
} // namespace detail  
} // namespace boost::parser  
  
#include <boost/parser/parser.hpp>  
```  
  
And then I could define it later after my parser class. Also, I didn't see anything in the documentation about adding a parser_interface and only managed to find that by reading through other parsers in the parser.hpp file.  
  
  
I think this could be avoided by adding a default noop print_parser (not sure what this is used for anyway). Or maybe being able to turn this feature off would be good.  
  
```  
template <typename Context>  
void print_parser(Context const&, auto const&, std::ostream&, int component = 0)  
{  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-29 20:37:50 UTC  
> Url: https://github.com/boostorg/parser/issues/178#issuecomment-2381594549  

This is by design.  The ability to be able to trace your parse is essential to writing anything but the simplest of parsers.  When you add a new custom parser, it is expected that you also add a function that enables its instrumentation.  If you make that a noop, well, "bad programmer!"
