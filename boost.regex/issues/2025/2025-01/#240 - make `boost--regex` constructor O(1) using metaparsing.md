# #240 - [Feature Request] make `boost::regex` constructor O(1) using metaparsing [Open]

> Username: denzor200  
> Created at: 2025-01-07 01:03:07 UTC  
> Updated at: 2025-01-07 01:03:07 UTC  
> Url: https://github.com/boostorg/regex/issues/240  

Constructors of `std::regex` and `boost::regex` are known to be slow for a big regular expression and of course nobody will use them inside any loop statement.  
  
Given the example..  
```  
void process()  
{  
   for (int i=0; i<10000; ++i) {  
      boost::regex r("([^[:blank:]]+)|(\"[^\"]+\")|(\\([^\\)]+\\))");  
      // ...  
   }  
}  
```  
..should be changed to..  
```  
boost::regex r("([^[:blank:]]+)|(\"[^\"]+\")|(\\([^\\)]+\\))");  
void process()  
{  
   for (int i=0; i<10000; ++i) {  
      // ...  
   }  
}  
```  
..due to performance reason.  
  
As we could see, the constructor performs parsing of string literal in run time and this is the bottleneck. We know that it's possible to parse any literal during compilation time, but at the current moment `boost::regex` unable to do it.  
  
I've found a library which was designed to write compile-time parsers:  
https://github.com/boostorg/metaparse  
I guess this library might be yet another dependency of the Boost Regex library, doesn't it?  
  
This library even contains sample of parsing simple regex during compilation time:  
https://github.com/boostorg/metaparse/blob/master/example/regexp/main.cpp  
  
I suppose we can extend that sample and then integrate it into `boost::regex`. Here we even no need to make `boost::regex` constexpr and all that we need is to add yet another overload of regex's constructor:  
```  
template<char... Chars>  
explicit basic_regexp(boost::metaparse::string<Chars...>,  
                      flag_type f = regex_constants::normal);  
```  
  
And then our example might be reimplemented like this:  
```  
void process()  
{  
   for (int i=0; i<10000; ++i) {  
      boost::regex r(BOOST_METAPARSE_STRING_VALUE("([^[:blank:]]+)|(\"[^\"]+\")|(\\([^\\)]+\\))"));  
      // ...  
   }  
}  
```  
No global variables anymore, and no performance issues, looks good.
