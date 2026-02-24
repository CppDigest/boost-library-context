# #223 - Containers, bp::None and alternative parsers behavior [Closed]

> Username: voivoid  
> Created at: 2025-04-22 07:35:13 UTC  
> Updated at: 2025-07-27 01:16:15 UTC  
> Closed at: 2025-07-27 01:16:15 UTC  
> Url: https://github.com/boostorg/parser/issues/223  

Please take a look at the following artificial example:  
  
```c++  
int main()  
{  
    namespace bp = boost::parser;  
  
    std::vector<char> v;  
    const auto parser = *(bp::lit('x') | bp::char_('y'));  
    bp::parse("xy", parser, bp::ws, v);      
      
    assert(v.size() == 1); // the assert fails since there are two elements in the vector: '\0' and 'y'. Seems pretty surprising to me  
}  
```  
  
But the behavior is different if the same parser is used with other bp::parse overload:  
```c++  
int main()  
{  
    namespace bp = boost::parser;  
  
    const auto parser = *(bp::lit('x') | bp::char_('y'));  
    const auto result = bp::parse("xy", parser, bp::ws);  
  
    assert(result->size() == 1); // success, the vector has only one 'y' element  
}  
```  
  
I guess it's worth noting that the very same x3 parser gives only one element in the vector too:  
  
```c++  
int main()  
{      
    namespace x3 = boost::spirit::x3;  
  
    std::string input = "xy";  
    std::vector<char> v;  
    const auto parser = *(x3::lit('x') | x3::char_('y'));  
    x3::parse(input.cbegin(), input.cend(), parser, v);  
  
    assert(v.size() == 1); // success, the vector has only one 'y' element  
}  
```  
  
  
  
Is the behavior in the first example ( the one with the failed assert) expected?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:19:57 UTC  
> Updated at: 2025-04-26 22:43:41 UTC  
> Url: https://github.com/boostorg/parser/issues/223#issuecomment-2832660075  

That certainly looks like a bug.  Does the `'\0'` come before the `'y'`?  
  
Oh, and in case it comes up, what compiler were you using?

---

## Comment 2

> Username: voivoid  
> Created at: 2025-04-27 21:20:16 UTC  
> Url: https://github.com/boostorg/parser/issues/223#issuecomment-2833645972  

Yes, the '\0' comes before the 'y'.  
  
I observe the issue in boost 1.88 with clang 19.1, gcc 14.2 and latest MSVC 19.44.
