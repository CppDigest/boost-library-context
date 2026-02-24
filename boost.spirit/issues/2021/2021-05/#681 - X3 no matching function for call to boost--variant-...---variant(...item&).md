# #681 - X3 no matching function for call to boost::variant<...>::variant(...item&) [Closed]

> Username: Bockeman  
> Created at: 2021-05-22 21:50:10 UTC  
> Updated at: 2021-05-24 16:20:16 UTC  
> Closed at: 2021-05-24 16:20:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/681  

[make.log](https://github.com/boostorg/spirit/files/6527211/make.log)  
[rgw29_minimal.cpp.txt](https://github.com/boostorg/spirit/files/6527213/rgw29_minimal.cpp.txt)  
  
> _Preface:_  
> _I realise this looks like a "please fix my code" request; it is!  But the main purpose is to expose one example of where a possible user coding error results in an error message that is undecipherable to the novice X3 user.  The intention of a library, like boost::spirit::x3, is to wrap complexity into an elegant, simple to use library (which it does very successfully).  But if this simple to use paradigm is broken by error messages that are undecipherable to the novice user, then IMHO the library is **broken**, in other words a bug._  
> _It may be that the underlying cause of this error message is indeed a bug in the code (though I doubt this).  Assuming not, and there being an error in my code: the short term fix would be simply to "fix my code" (sorry!), but at least that would provide an answer to others who might stumble similarly.  The more robust fix would be to add this example to the documentation, or even better, to adjust the code such that the trigger for this type of error is caught before the compiler spews copious templated error messages and presents the error in a way that is meaningful to the user_  
> _I believe that I have extracted the pertinent information from the 533 line, 161kb error message.  I think I understand that it is telling me that there is a missing constructor.  But the X3 documentation does not provide an example similar to this, so I have no clue as to the form of and how to add this missing constuctor to my user level code._  
  
I have a very simple parser with 4 rules, one of which is [1]:  
```  
auto const start_rule_def     = +(gap | +(char_ -'{' -space));  
```  
in other words, capture "gaps" (which include spaces and braced "comments") and everythings which is not a "gap", in a parser without any skip parser.  
  
I would like to wrap this construct for use elsewhere, so I created a separate "catch_all" rule [2]:  
```  
  auto const catch_all_def      = gap | +(char_ -'{' -space);  
  auto const start_rule_def     = +catch_all;  
```  
Notice that the content of the rule (inside the +(...) ) is identical in both cases. So both should do exactly the same thing.  
  
The erudite reader will of course realise that this code is a trivialization of my real world scenario.  
  
However, [1] compiles successfully, whereas [2] generates compile errors (see attached make.log), and I believe the pertinent line is:  
```  
src/rgw29_minimal.cpp:137:60:   required from here  
/usr/include/boost/spirit/home/x3/support/ast/variant.hpp:151:39: error: no matching function for call to ??boost::variant<minimal::ast::gap, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >, minimal::ast::catch_all>::variant(minimal::ast::catch_all_item&)??  
  151 |             : var(std::forward<T>(rhs)) {}  
      |                                       ^  
```  
which I interpret to mean that there is a missing constructor of the form:  
```  
 boost::variant<...>::variant(...item&)  
```  
  
I don't think the problem is in the parser rules, but rather the ast.  But I cannot see what is wrong with my ast:  
```  
  struct start_rule_item  
  : x3::variant  
  < catch_all  
  >    
  {  
    using base_type::base_type;  
    using base_type::operator=;  
  };  
```  
I accept this variant has only one element, but that is not the case in the real world scenario, and I don't see why this should cause a problem.  In the MCVE, attached, there are a few more variants for good measure.  
  
Please could you kindly help me to interpret this error message and, if possible, (assuming a coding shortcoming on my part) help me and others from falling into the same trap.  Thanks in advance.

---

## Comment 1

> Username: Bockeman  
> Created at: 2021-05-23 23:43:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/681#issuecomment-846644658  

In the rgw29_minimal.cpp.txt there is a way to make this compile:  
```  
//#define WITHOUT_CATCH_ALL           // This code compiles successfully when this is uncommented.  
```  
and this essentially removes the ```catch_all``` rule and replaces it with the contents in-line where ```catch_all``` is used.  
```  
  #ifdef WITHOUT_CATCH_ALL      // This selection, an expansion, results in a successful compile.  
  auto const start_rule_def     = +(gap | +(char_ -'{' -space));                // catch_all expanded  
  #else                         // This selection results in compile error messages  
  auto const start_rule_def     = +catch_all;                                   // catch_all as separate rule  
  #endif                        // but the catch_all rule is identical to the working expansion.  
```  
  
To be clear, this is **not** a work around but rather a demonstration of what might be the hub of an underlying bug or flawed user coding.  The real problem, perhaps not very clear from the minimal example, is that I have many more cases like ```catch_all``` and without the modularisation afforded by separate or distinct smaller rules, the overall code would contain rules that  were impossibly large and difficult to get right, debug and so on.

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-05-24 16:20:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/681#issuecomment-847158965  

Duplicate of https://github.com/boostorg/spirit/issues/679#issuecomment-846272130
