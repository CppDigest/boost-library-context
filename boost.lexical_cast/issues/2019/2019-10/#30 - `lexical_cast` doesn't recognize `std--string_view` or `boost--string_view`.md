# #30 - `lexical_cast` doesn't recognize `std::string_view` or `boost::string_view` [Closed]

> Username: pdimov  
> Created at: 2019-10-29 21:39:06 UTC  
> Updated at: 2024-03-14 00:12:02 UTC  
> Closed at: 2024-02-04 10:02:00 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/30  

As per title; `lexical_cast` has specializations for string types, but not for string view types. Reported on the C++ Slack.

---

## Comment 1

> Username: sehe  
> Created at: 2022-02-08 22:32:12 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/30#issuecomment-1033129523  

Is this still current? I have just used `boost::convert::try_lexical_convert` just fine with both `std::string_view` and `boost::iterator_range<>`, so it would seem this has been fixed somewhere before 1.78.0?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-02-08 23:01:50 UTC  
> Updated at: 2022-02-08 23:52:28 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/30#issuecomment-1033147368  

I think that it's still current, yes. Looking at https://godbolt.org/z/WMa7vhnT9, the function `f1` that uses a string  
```  
int f1()  
{  
    std::string s1( "5" );  
    return boost::lexical_cast<int>( s1 );  
}  
```  
goes directly to `boost::detail::lcast_ret_unsigned<std::char_traits<char>, unsigned int, char>::convert()`, whereas the two functions using string views  
```  
int f2()  
{  
    std::string_view s1( "5" );  
    return boost::lexical_cast<int>( s1 );  
}  
  
int f3()  
{  
    boost::string_view s1( "5" );  
    return boost::lexical_cast<int>( s1 );  
}  
```  
take the generic path using a stream.  
  
Similarly, in https://godbolt.org/z/9W6M1c35e, `f1`  
```  
std::string f1()  
{  
    std::string s1( "5" );  
    return boost::lexical_cast<std::string>( s1 );  
}  
```  
does a straight string copy, whereas `f2` and `f3`  
```  
std::string f2()  
{  
    std::string_view s1( "5" );  
    return boost::lexical_cast<std::string>( s1 );  
}  
  
std::string f3()  
{  
    boost::string_view s1( "5" );  
    return boost::lexical_cast<std::string>( s1 );  
}  
```  
go through the slow stream path.

---

## Comment 3

> Username: sehe  
> Created at: 2022-02-08 23:47:28 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/30#issuecomment-1033175700  

Ah. I completely misunderstood the problem description then. It's not that the conversions aren't supported, it's that they don't generate optimal code. The negative examples might actually be good addition for the original description.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-02-08 23:53:38 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/30#issuecomment-1033178766  

Edited to supply the code examples in case the CE links disappear.

---

## Comment 5

> Username: apolukhin  
> Created at: 2024-02-04 10:02:00 UTC  
> Updated at: 2024-02-04 10:02:22 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/30#issuecomment-1925671567  

Merged to master branch.  
  
Many thanks for the bug report!
