# #58 - Not supporting en_001, en_150 or en_US_POSIX locale [Closed]

> Username: mattjgalloway  
> Created at: 2020-07-17 20:37:44 UTC  
> Updated at: 2023-03-01 20:20:47 UTC  
> Closed at: 2023-03-01 20:19:29 UTC  
> Url: https://github.com/boostorg/locale/issues/58  

I'm starting to use Boost.Locale in a project and I've hit up against a problem when the system locale is en_001. And I believe it will also be a problem with en_150 and en_US_POSIX as well.  
The problem is best described with the following test case:  
  
```  
#include <boost/locale.hpp>  
#include <iostream>  
  
int main(int argc, char** argv) {  
  boost::locale::generator gen;  
  std::locale loc(gen(""));  
  std::locale::global(loc);  
  std::cout.imbue(loc);  
  
  std::cout << "LOCALE NAME: " << std::use_facet<boost::locale::info>(loc).name() << std::endl;  
  std::cout << "LOCALE LANG: " << std::use_facet<boost::locale::info>(loc).language() << std::endl;  
  std::cout << "LOCALE COUNTRY: " << std::use_facet<boost::locale::info>(loc).country()  
            << std::endl;  
  std::cout << "LOCALE ENCODING: " << std::use_facet<boost::locale::info>(loc).encoding()  
            << std::endl;  
  std::cout << "LOCALE UTF8: " << std::use_facet<boost::locale::info>(loc).utf8() << std::endl;  
  
  return 0;  
}  
```  
  
If the system is in en_001, which on Windows is the "English (World)" region name, then the following will be output:  
  
```  
LOCALE NAME: en_001.UTF-8  
LOCALE LANG: en  
LOCALE COUNTRY:  
LOCALE ENCODING: us-ascii  
LOCALE UTF8: 0  
```  
  
I would expect the output to be:  
```  
LOCALE NAME: en_001.UTF-8  
LOCALE LANG: en  
LOCALE COUNTRY: 001  
LOCALE ENCODING: utf-8  
LOCALE UTF8: 1  
```  
  
It's coming from the fact that in `boost::locale::util::locale_data::parse_from_country`, we are assuming the country needs to contain only 'a' to 'z' or 'A' to 'Z'. But `en_001` (and `en_150`) are valid locales. Probably `en_US_POSIX` should be handled separately as it's special.

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-02-16 18:02:28 UTC  
> Url: https://github.com/boostorg/locale/issues/58#issuecomment-1433497692  

Hi @mattjgalloway . I'm currently working on getting your PR fixing this into the next release (no worries about the conflicts, I'll resolve those)  
  
I was wondering whether you had any expectation on how `en_US_POSIX` is handled?     
As far as I've understood this is basically the `"C"` locale in C++, aka `"POSIX"`. So I think it makes sense to treat it as an alias so the output would be:  
  
```  
LOCALE NAME: C  
LOCALE LANG:  
LOCALE COUNTRY:  
LOCALE ENCODING:  
LOCALE UTF8: 0  
```  
  
You could run into this on Linux or when using `boost::locale::generator("en_US_POSIX")` as the WinAPI will not return that as the "system locale".

---

## Comment 2

> Username: mattjgalloway  
> Created at: 2023-02-16 22:51:36 UTC  
> Url: https://github.com/boostorg/locale/issues/58#issuecomment-1433844806  

@Flamefire Thanks for coming back on this!  
  
Yes I think that would be right for `en_US_POSIX`.

---

## Comment 3

> Username: mattjgalloway  
> Created at: 2023-03-01 20:20:47 UTC  
> Url: https://github.com/boostorg/locale/issues/58#issuecomment-1450795744  

Thanks @Flamefire for getting this merged!
