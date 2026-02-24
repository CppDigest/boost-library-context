# #68 - boost::regex_search contradicts std::regex_search in changing match_result argument [Open]

> Username: Artalus  
> Created at: 2018-09-17 12:36:05 UTC  
> Updated at: 2018-09-19 04:03:11 UTC  
> Url: https://github.com/boostorg/regex/issues/68  

```  
#include <iostream>  
#include <regex>  
#include <boost/regex.hpp>  
#include <string>  
    
std::string target("821-xxxzzz");  
void st() {  
    std::smatch sm;  
    std::regex re1("^.+\\/(\\d+)$");  
    std::regex_search(target, sm, re1);  
    std::cout << "after std search, smatch is " << sm.size() << std::endl;  
}  
void bst() {  
    boost::smatch sm;  
    boost::regex re1("^.+\\/(\\d+)$");  
    boost::regex_search(target, sm, re1);  
    std::cout << "after boost search, smatch is " << sm.size() << std::endl;  
}  
  
int main() {  
    st();  
    bst();  
    return 0;  
}  
```  
```  
after std search, smatch is 0  
after boost search, smatch is 2  
```  
STD (cppreference.com) says:  
`the object match is updated as follows: If the match does not exist: match.ready() == true; match.empty() == true; match.size() == 0`  
While Boost docs says:  
`Postconditions: If the function returns false, then the effect on parameter match is undefined`  
  
This assumption contradicts with the logic and expectations of end-user, that if no match was found in text, then the `boost::match_result` object state would represent it through `empty` and `size` methods. Everything said is related to `boost::regex_match` too.

---

## Comment 1

> Username: degski  
> Created at: 2018-09-19 04:03:11 UTC  
> Url: https://github.com/boostorg/regex/issues/68#issuecomment-422643460  

@Artalus I was going to say to file an issue at `boost::regex`, but then I saw you already did. I'm just writing this so others know you files the issue as well. You could just close it here.
