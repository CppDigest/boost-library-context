# #177 - Top-level recursion ignores top-level alternatives [Open]

> Username: Davidebyzero  
> Created at: 2022-07-16 17:43:14 UTC  
> Updated at: 2022-07-16 19:20:15 UTC  
> Url: https://github.com/boostorg/regex/issues/177  

A top-level recursive call, i.e. `(?R)` or `(?0)`, only uses the first alternative, and ignores all subsequent ones.  
  
As a simple example, `a(?R)|z` will only match `z`, but it is supposed to be able to also match `az`. The workaround is to use `(?:a(?R)|z)` or `(a(?R)|z)` or `(a(?1)|z)`, which can match either `az` or `z`.  
  
The regex which actually led to discovery of this bug was [(?=(xx+?)\1*(?=\1$)((?R)))(?=(x+)\3*(?=\3$)((?R)))\2\4|x\B(?R)|](https://codegolf.stackexchange.com/questions/79644/how-many-steps-does-it-take-from-n-to-1-by-subtracting-the-greatest-divisor/249872#249872), which calculates the [OEIS A064097](https://oeis.org/A064097) number sequence in unary. But to make it work in the current version of Boost, the entire regex must be enclosed in parentheses.  
  
I have tested and confirmed this to be happening in the latest version of Notepad++, [v8.4.4 (released yesterday)](https://notepad-plus-plus.org/downloads/v8.4.4/), which uses presumably the latest (or at least a very recent) version of Boost as its regex engine.  
  
Sample program demonstrating the bug:  
  
```  
#include <boost/regex.hpp>  
#include <iostream>  
int main()  
{  
    boost::smatch what;  
    if (boost::regex_search(std::string("az"), what, boost::regex(   "a(?R)|z" ))) std::cout << what[0] << '\n';  
    if (boost::regex_search(std::string("az"), what, boost::regex("(?:a(?R)|z)"))) std::cout << what[0] << '\n';  
    if (boost::regex_search(std::string("az"), what, boost::regex(  "(a(?1)|z)"))) std::cout << what[0] << '\n';  
    return 0;  
}  
```  
  
This should print three identical lines of `az`, but instead prints `z` followed by two lines of `az`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-07-16 19:20:14 UTC  
> Url: https://github.com/boostorg/regex/issues/177#issuecomment-1186271022  

Thanks for the report, will investigate shortly!
