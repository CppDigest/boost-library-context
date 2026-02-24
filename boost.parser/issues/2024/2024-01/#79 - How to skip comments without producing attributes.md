# #79 - How to skip comments without producing attributes? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-27 08:17:52 UTC  
> Updated at: 2024-01-27 09:16:35 UTC  
> Closed at: 2024-01-27 09:16:35 UTC  
> Url: https://github.com/boostorg/parser/issues/79  

This is more a request for an advice.  
  
I want to parse a sequence of `int`s, skipping potential comments, and produce a `vector<int>`. Here's a sample input:  
```  
3 [comment][comment] 4 [comment]  
```  
  
I built the following parser:  
  
```c++  
const bp::rule<struct comment_tag> comment = "comment";  
const auto comment_def = '[' >> *(bp::char_ - ']') >> ']';  
BOOST_PARSER_DEFINE_RULES(comment);  
auto parser = +(bp::int_ | comment);  
```  
  
But it doesn't do the trick: it puts a `0` to my vector upon every encountered comment. Boost.Spirit, due to its clever attribute transformation does exactly what I need.  
  
Full example:  
https://godbolt.org/z/vsa5eK5Gx  
  
Is there a way to write a parser for that that does not involve custom actions?  
  
You can consider it a suggestion to document the differences between Boost.Spirit parsers and Boost.Parser parsers.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-01-27 08:19:33 UTC  
> Url: https://github.com/boostorg/parser/issues/79#issuecomment-1913074351  

Also, why does it arbitrarily choose to produce value `0`?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-27 09:15:11 UTC  
> Url: https://github.com/boostorg/parser/issues/79#issuecomment-1913088908  

Skipper, fool!  
https://godbolt.org/z/dTcbqdzMK  
  
I still wonder about the zero, though.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-27 09:15:55 UTC  
> Url: https://github.com/boostorg/parser/issues/79#issuecomment-1913089152  

> Also, why does it arbitrarily choose to produce value `0`?  
  
First, this question.  It produces an int, value-initialized, because you told it to. :)  `int_ | comment` has the attribute type `int`, and always succeeds.  If it matches an int, it's the value of that int.  If it does not, it's the initial value of the attribute, which as I said is value-initialized, or `0` for an `int`.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-01-27 09:16:25 UTC  
> Url: https://github.com/boostorg/parser/issues/79#issuecomment-1913089301  

> Skipper, fool! https://godbolt.org/z/dTcbqdzMK  
>   
> I still wonder about the zero, though.  
  
I wasn't going to call you a fool, but this is exactly right! :)
