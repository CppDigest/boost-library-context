# #182 - Regex bug  - possibly due to non-greedy dot capture [Closed]

> Username: nikess  
> Created at: 2022-10-25 08:37:12 UTC  
> Updated at: 2024-03-28 12:58:38 UTC  
> Closed at: 2024-03-28 12:58:38 UTC  
> Url: https://github.com/boostorg/regex/issues/182  

Boost 1.80.   
  
Possibly the cause is something to do with whitespace handling, possibly the non-greedy.  
  
Regex = "oid_lo=(.*?) "  
  
For input data "oid_lo=123 oid_ex=455"  
:  
  
```  
boost::regex regex_oid_lo("oid_lo=(.*?) "s);  
boost::smatch regex_oid_lo_matches;  
if (boost::regex_search("oid_lo=123 oid_ex=455"s, regex_oid_lo_matches, regex_oid_lo)) {  
  
        cout << regex_oid_lo_matches.size() << endl;  
        cout << regex_oid_lo_matches[0] << endl;  
        cout << regex_oid_lo_matches[1] << endl;  
    }else{  
        cout << "no match" << endl;  
}  
```  
  
This returns true for the regex_search, and yet regex_oid_lo_matches is not populated correctly - it contains garbage. Different every run.  
  
Expected: returns true for search, and regex_oid_lo_matches[1] == "123".  
  
  
For input data "oid_lo=123 " it works as expected, matching oid_lo=123 correctly and "123" being in regex_oid_lo_matches[1]  
  
```  
boost::regex regex_oid_lo("oid_lo=(.*?) "s);  
boost::smatch regex_oid_lo_matches;  
if (boost::regex_search("oid_lo=123 "s, regex_oid_lo_matches, regex_oid_lo)) {  
  
        cout << regex_oid_lo_matches.size() << endl;  
        cout << regex_oid_lo_matches[0] << endl;  
        cout << regex_oid_lo_matches[1] << endl;  
    }else{  
        cout << "no match" << endl;  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-28 12:58:38 UTC  
> Url: https://github.com/boostorg/regex/issues/182#issuecomment-2025124828  

There's a bug in your code: the `boost::smatch` is populated with iterators into the string being matched, but the lifetime of your string does not extend beyond the function call, so the iterators point to invalid (freed) memory.
