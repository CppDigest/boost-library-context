# #88 - regex_replace duplicating trailing replace token [Closed]

> Username: cedral  
> Created at: 2019-09-24 18:51:44 UTC  
> Updated at: 2020-01-22 18:34:03 UTC  
> Closed at: 2020-01-22 18:26:45 UTC  
> Url: https://github.com/boostorg/regex/issues/88  

The following code should result in "moreless" but instead results in "morelessless"  
  
```  
#include <iostream>  
#include <string>  
#include <boost/regex.hpp>  
  
int main(int argc, char **argv)  
{  
  boost::regex::flag_type regex_flags = boost::regex_constants::normal;  
  boost::regex_constants::match_flag_type match_flags = boost::regex_constants::match_default;  
  boost::regex pattern("(.*)", regex_flags);  
  std::string strResult;  
  strResult = boost::regex_replace(std::string("more"), pattern, std::string("${1}less"), match_flags);  
  std::cout << strResult << std::endl;  
  char result;  
  std::cin >> result;  
  return 0;  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-22 18:26:45 UTC  
> Url: https://github.com/boostorg/regex/issues/88#issuecomment-577320804  

Nope, perl does the same: there is a zero width match after the first match of "more".  
  
Try:  
  
```  
$string = "more";  
$string =~ s/(.*)/$1less/g;  
print $string;  
```

---

## Comment 2

> Username: cedral  
> Created at: 2020-01-22 18:30:43 UTC  
> Url: https://github.com/boostorg/regex/issues/88#issuecomment-577322500  

Thanks for letting me know. That still seems broken but at least it's not specific to boost.

---

## Comment 3

> Username: cedral  
> Created at: 2020-01-22 18:33:26 UTC  
> Url: https://github.com/boostorg/regex/issues/88#issuecomment-577323514  

no, I see what you are saying. .* can match zero characters. The solution is to use (.+).

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-01-22 18:34:02 UTC  
> Url: https://github.com/boostorg/regex/issues/88#issuecomment-577323764  

Correct.
