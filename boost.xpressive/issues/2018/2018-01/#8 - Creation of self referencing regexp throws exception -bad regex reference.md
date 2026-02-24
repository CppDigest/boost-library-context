# #8 - Creation of self referencing regexp throws exception "bad regex reference" [Open]

> Username: Ramambahara  
> Created at: 2018-01-04 19:15:59 UTC  
> Updated at: 2019-02-25 08:44:25 UTC  
> Url: https://github.com/boostorg/xpressive/issues/8  

Hi.   
I'm trying to create composite regular expression by appending pieces one at a time.   
  
Example:  
  
```c++  
#include <boost/xpressive/xpressive.hpp>  
  
#include <iostream>  
#include <string>  
  
int main()  
{  
  using namespace boost::xpressive;  
  using namespace std::string_literals;  
  auto regex = sregex{ bos >> as_xpr("foo") >> *_ };  
  
  regex = regex | sregex{ bos >> as_xpr("bar") >> *_ };  
  
  std::cout << regex_match("foololo"s, regex) << std::endl;  
  std::cout << regex_match("barlolo"s, regex) << std::endl;  
  
  return 0;  
}  
```  
  
I get exception from ` regex = regex | sregex{ bos >> as_xpr("bar") >> *_ };`. It seems that inside of assignment it enters `boost::xpressive::detail::regex_matcher::static_compile_impl2(...)`, does `impl->tracking_clear();` and partially clears contents of the temporary object on the right hand side of assignment operator.  
  
It seems to me that my case is pretty similar to the case described in the [Embedding a Regex by Value](http://www.boost.org/doc/libs/1_65_1/doc/html/xpressive/user_s_guide.html#boost_xpressive.user_s_guide.grammars_and_nested_matches.embedding_a_regex_by_value) example. I am able to reproduce this behavior for boost 1.65.1 and 1.66.0 on both MSVC 2015 sp1 with /std:c++=latest and gcc 7.2.0.  
  
How should I create such composite regular expression assuming I don't have all the pieces at once?

---

## Comment 1

> Username: brightshine1111  
> Created at: 2019-02-22 00:48:01 UTC  
> Url: https://github.com/boostorg/xpressive/issues/8#issuecomment-466228497  

I've also run into this exact problem and I'm still hunting for a solution. Any help would be appreciated!

---

## Comment 2

> Username: brightshine1111  
> Created at: 2019-02-25 03:38:44 UTC  
> Url: https://github.com/boostorg/xpressive/issues/8#issuecomment-466861152  

I found a workaround. The issue seems to be with _directly_ self-referencing a regex. If you copy a regex to a temporary variable first, you can then use that temp variable when updating the original regex. Example:  
  
```  
sregex rx1 = sregex::compile(".example");  
sregex t = rx1;  
rx1 = bos >> t >> eos;  
```  
  
I would guess there's some optimization in newer versions of gcc that's messing with the self-reference ability of the syntax.

---

## Comment 3

> Username: Ramambahara  
> Created at: 2019-02-25 08:44:25 UTC  
> Url: https://github.com/boostorg/xpressive/issues/8#issuecomment-466920605  

Thank you a lot, it might also work for me!
