# #134 - Docs say 'define' when they probably meant to say 'declare' [Closed]

> Username: LegalizeAdulthood  
> Created at: 2024-02-28 16:43:19 UTC  
> Updated at: 2024-02-28 23:34:43 UTC  
> Closed at: 2024-02-28 23:34:43 UTC  
> Url: https://github.com/boostorg/parser/issues/134  

At the top of the [tutorial page on rule parsers](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/rule_parsers.html):  
  
> Here is how we define a [rule](https://tzlaine.github.io/parser/doc/html/boost/parser/rule.html), which is analogous to forward declaring a function:  
  
This should say  
  
> Here is how we declare a [rule](https://tzlaine.github.io/parser/doc/html/boost/parser/rule.html), which is analogous to forward declaring a function:

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-28 23:34:43 UTC  
> Url: https://github.com/boostorg/parser/issues/134#issuecomment-1970094803  

The line in question is this.  
  
```c++  
bp::rule<struct doubles, std::vector<double>> doubles = "doubles";  
```  
  
That is most definitely a declaration *and* a definition.
