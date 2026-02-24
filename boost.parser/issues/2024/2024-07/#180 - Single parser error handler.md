# #180 - Single parser error handler [Closed]

> Username: asolwa  
> Created at: 2024-07-01 15:56:17 UTC  
> Updated at: 2024-10-03 01:44:10 UTC  
> Closed at: 2024-10-03 01:44:10 UTC  
> Url: https://github.com/boostorg/parser/issues/180  

When a single, primitive parser is used, the error stream remains empty, even if an error occurs.  
In the example below, 'oss' is empty, but it should have an error message, shouldn't it?  
```  
#include <boost/parser/parser.hpp>  
  
int main()  
{  
    using namespace boost::parser;  
  
    constexpr auto parser = string("test");  
    {  
        char const * str = "abz";  
        std::ostringstream oss;  
        stream_error_handler eh("simple_parser.cpp", oss);  
        auto result = parse(str, with_error_handler(parser, eh));  
        std::cout << oss.str();  
    }  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-10-03 01:44:10 UTC  
> Url: https://github.com/boostorg/parser/issues/180#issuecomment-2390332797  

The only time that error handler should have anything in it is when: 1) you, the user, put something in there via a semantic action; or 2) you, the user, indicate where hard errors occur ("expectation points"), and then one of those expectation points is violated.  See the "Error Handling and Debugging" page of the docs, in particular the section "How diagnostics are generated".  
  
The fact that its a primitive parser is a red herring, btw.  The same thing will happen with any parser that does not do 1) or 2).
