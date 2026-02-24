# #36 - How does parsing to `vector<string>` work? [Closed]

> Username: akrzemi1  
> Created at: 2023-12-29 22:29:12 UTC  
> Updated at: 2024-01-03 08:08:59 UTC  
> Closed at: 2024-01-03 08:08:59 UTC  
> Url: https://github.com/boostorg/parser/issues/36  

In the following piece of code the goal is to parse a sequence of identifiers -- like `"id1 id_ idx"` -- into a `vector<string>`:  
  
```c++  
void parse_sequence_of_identifiers()  
{  
  namespace bp = boost::parser;  
  
  auto id = bp::lexeme[+(bp::ascii::alnum | bp::char_('_'))];   
  auto ids = +id;  
  
  std::string str;  
  std::vector<std::string> vec;  
    
  bp::parse("id1", id, bp::ascii::space, str);      // (1)  
  bp::parse("id1 id2", ids, bp::ascii::space, vec); // (2)  
  bp::parse("i1 i2", ids, bp::ascii::space, str);   // (3)  
}  
```  
  
(1) works fine. (2) fails to compile. (3) compiles and reports success, but the result is an empty string. This goes surely against the intuitive expectations.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2023-12-29 23:02:29 UTC  
> Url: https://github.com/boostorg/parser/issues/36#issuecomment-1872382161  

I should have mentioned that (2) above works as expected in Boost.Spirit: https://godbolt.org/z/KsPxnnenz

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-01-03 00:00:41 UTC  
> Url: https://github.com/boostorg/parser/issues/36#issuecomment-1874701739  

Thanks for catching this.  Clearly I did not test enough cases.  (1) and (2) should work as of the patches that I just pushed to develop (if everything is fine, they'll land on master soonish, with a doc update).  (3) is a bone-headed mistake on my part that breaks probably everything if supported.  I've left (3) ill-formed for now.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-01-03 06:30:39 UTC  
> Url: https://github.com/boostorg/parser/issues/36#issuecomment-1874905209  

Thanks. This Issue is actually about (2).   
  
Regarding (3), I would never expect it to work. I was actually surprised that it compiled. If it now fails to compile, then I am happy.
