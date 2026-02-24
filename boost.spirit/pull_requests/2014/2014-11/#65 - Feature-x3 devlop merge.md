# #65 Feature/x3 devlop merge [Merged]

> Username: teajay-fr  
> Created at: 2014-11-01 21:55:27 UTC  
> Updated at: 2014-11-01 23:31:38 UTC  
> Merged at: 2014-11-01 23:31:38 UTC  
> Closed at: 2014-11-01 23:31:38 UTC  
> Url: https://github.com/boostorg/spirit/pull/65  

This is the merge pull request to pull all the changes left behind in the x3-devel branch to the develop branch.  
  
I had to add support for the no_case directive to the numeric/bool parsers for everything to work.   
The bool parsers require the character encoding information, which can be passed,   
- on the parser level (the current implementation)  
- on the policy level  
  
Both would work, it's only an API definition choice.

---
