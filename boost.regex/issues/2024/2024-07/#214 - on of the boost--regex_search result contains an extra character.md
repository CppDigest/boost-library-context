# #214 - on of the boost::regex_search result contains an extra character [Open]

> Username: jszirani  
> Created at: 2024-07-08 16:45:29 UTC  
> Updated at: 2024-07-12 15:51:16 UTC  
> Url: https://github.com/boostorg/regex/issues/214  

The issue was found in some custom URL parsing code, I am not sure this is a known issue.   
  
I've isolated the issue to this tiny bit of code:  
  
```         
static boost::regex pattern(R"((&?([^&=]+)(=?([^&]*))))",   
                            boost::regex_constants::extended | boost::regex_constants::optimize);  
boost::smatch matches;  
std::string testString = "SERVICE=WMTS&TileCol=1";  
if (boost::regex_search(testString.cbegin(), testString.cend(), matches, pattern)) {  
  for (unsigned int i = 0; i < matches.size(); ++i) {  
    std::cout << i << ": " << std::string(matches[i].first, matches[i].second) << std::endl;  
  }  
}  
```  
  
this will print   
  
`0: SERVICE=WMTS  
1: SERVICE=WMTS  
2: SERVICE  
3: =WMTS  
4: =WMTS`  
  
but I think the last item (number 4) should not have the '=' character present, you can check the regular expression on https://regex-vis.com/ that the = is out of group 4  
  
if you switch to std regex  
  
```  
static std::regex pattern(R"((&?([^&=]+)(=?([^&]*))))",   
                          std::regex_constants::extended | std::regex_constants::optimize);  
std::smatch matches;  
std::string testString = "SERVICE=WMTS&TileCol=1";  
if (std::regex_search(testString.cbegin(), testString.cend(), matches, pattern)) {  
  for (size_t i = 0; i < matches.size(); ++i) {  
    std::cout << i << ": " << std::string(matches[i].first, matches[i].second) << std::endl;  
  }  
}  
```  
  
it will print   
  
`0: SERVICE=WMTS  
1: SERVICE=WMTS  
2: SERVICE  
3: =WMTS  
4: WMTS`

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-07-12 15:51:15 UTC  
> Url: https://github.com/boostorg/regex/issues/214#issuecomment-2225868906  

Well... I think we're correct here: you've asked for POSIX extended regular expressions which means "leftmost longest" and that rule applies to sub-expressions as well.  In this case the matcher finds the match you were expecting first (and this is what is returned had you specified a perl regular expression), then rather than stopping at that point as a perl matcher would do, it backtracks looking for something "better".  The first backtrack tries matching with the `=?` part matching zero times, and low and behold the `[^&]*` then matches to the next `&` just like before, only this time it starts further left and so is considered "better".  Hope that makes sense!
