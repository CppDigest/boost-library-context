# #147 - When using match_extra with regex_iterator captures from previous iterations are incorrectly preserved [Open]

> Username: xentrax  
> Created at: 2021-08-03 21:02:56 UTC  
> Updated at: 2021-08-04 19:35:56 UTC  
> Url: https://github.com/boostorg/regex/issues/147  

boost::regex has a **wonderful** feature  boost::regex_constants::match_extra. It works well.  
But when using regex_iterator the contents of the captures() collection is not cleared before running next regex_search.  
regex_iterator_implementation::next() should clear all captures (capture_sequence_type) before invoking next regex_search.  
(( As a workaround get_captures() method can be used to clear captures for all submatches from the calling code on each iteration))  
  
To demonstate the problem the following code will unexpectedly output   
123456321789123654  
instead of   
123456789  
  
```  
#include <iostream>  
#include <string>  
#define BOOST_REGEX_MATCH_EXTRA  
#include <boost/regex.hpp>  
  
int main()  
{  
      
    std::string str = "BEG123END BEG456END   BEG789END";  
    boost::regex r("BEG(?:(?<group>\\d))+END");  
  
    boost::sregex_iterator beg(str.cbegin(), str.cend(), r, boost::regex_constants::match_extra);  
    for (boost::sregex_iterator i = beg; i != boost::sregex_iterator(); ++i)  
    {  
        const boost::ssub_match::capture_sequence_type& captures = (*i)["group"].captures();  
        for (const boost::ssub_match& c : captures)  
        {  
            std::cout << c.str();  
        }  
    }  
}  
```

---

## Comment 1

> Username: xentrax  
> Created at: 2021-08-04 19:34:46 UTC  
> Updated at: 2021-08-04 19:35:56 UTC  
> Url: https://github.com/boostorg/regex/issues/147#issuecomment-892919953  

Another issue with current (1.76) regex_iterator_implementation::next() implementation is that memory consumption grows exponentially with every iteration and quickly exhausts all avaialble memory and beyond. Looks like a logical memory leak.  
I had to patch the next() method to fix it.  
After I started resetting 'what' memeber variable upon every iteration all problems went away.  
  
```  
   bool next()  
   {  
      //if(what.prefix().first != what[0].second)  
      //   flags |= match_prev_avail;  
      BidirectionalIterator next_start = what[0].second;  
      match_flag_type f(flags);  
      if(!what.length() || (f & regex_constants::match_posix))  
         f |= regex_constants::match_not_initial_null;  
      //if(base != next_start)  
      //   f |= regex_constants::match_not_bob;  
  
      what = match_results<BidirectionalIterator>(); // reset to free up memory  
  
      bool result = regex_search(next_start, end, what, re, f, base);  
      if(result)  
         what.set_base(base);  
      return result;  
   }  
  
```
