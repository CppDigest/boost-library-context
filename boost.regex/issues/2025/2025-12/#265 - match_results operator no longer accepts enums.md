# #265 - match_results operator[] no longer accepts enums [Open]

> Username: ymett  
> Created at: 2025-12-11 09:15:21 UTC  
> Updated at: 2025-12-11 09:15:21 UTC  
> Url: https://github.com/boostorg/regex/issues/265  

Pull request [208 Suppress conversion warnings in match_results](https://github.com/boostorg/regex/pull/208) broke the ability to pass an unscoped enum to match_results operator[] (and related functions).  
  
This ability was particularly useful because it allowed giving expressive names to matches without having to number them individually (which is error prone).  
  
E.g.:  
  
enum { banana = 1, apple, orange };  
boost::regex r("(banana)|(apple)|(orange)");  
  
allowing  
match[apple]  
  
with the index value updating automatically when changing the regex (so long as the enum is updated to match).  
  
Two possible fixes:  
1. Quick: Add an explicit test for enums  
2. Correct: Allow anything which converts implicitly to int, restoring earlier behaviour
