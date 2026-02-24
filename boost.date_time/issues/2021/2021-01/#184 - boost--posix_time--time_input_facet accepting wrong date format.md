# #184 - boost::posix_time::time_input_facet accepting wrong date format [Open]

> Username: rbroggi  
> Created at: 2021-01-05 07:46:23 UTC  
> Updated at: 2021-11-26 15:47:43 UTC  
> Url: https://github.com/boostorg/date_time/issues/184  

Hello,   
  
(boost version: 1.68.0)  
  
While using the boost::posix_time::time_input_facet to read a custom format string, the read completes with a totally invalid string:  
  
E.g.:   
```cpp  
boost::posix_time::ptime fromISO8601ToPtime(const std::string& iISO8601Str) noexcept try {  
  std::stringstream ss;  
  ss.exceptions(std::ios_base::failbit);  
  ss.imbue(std::locale(std::locale::classic(), new boost::posix_time::time_facet("%Y-%m-%dT%H:%M:%SZ")));  
  ss.imbue(std::locale(ss.getloc(), new boost::posix_time::time_input_facet("%Y-%m-%dT%H:%M:%SZ")));  
  boost::posix_time::ptime aTimeToReturn;  
  ss << iISO8601Str;  
  ss >> aTimeToReturn;  
  return aTimeToReturn;  
}  
catch (std::exception& e) {  
  // default constructor creates a non-valid ptime  
  return boost::posix_time::ptime();  
}  
catch (...) {  
  // default constructor creates a non-valid ptime  
  return boost::posix_time::ptime();  
}  
```  
  
If I pass for instance the following **invalid** inputs to this method it will create a valid ptime:  
  
"2020-12-30T25:52:28Z" -> invalid due to the hours (25 should not be valid), leads to ptime: 2020-Dec-31 01:52:28  
"2020-12-30T16:62:28Z" -> minutes should be < 60, leads to ptime: 2020-Dec-30 17:02:28  
"2020-12-30T16:59:75Z" -> seconds should be < 60, leads to ptime: 2020-Dec-30 17:00:15  
"202001-04T23:52:28Z" -> does not conform with the facet in method, leads to ptime: 2020-Jan-04 23:52:28  
"4323" -> no idea why this translates to a valid input, leads to ptime: 4323-Jan-01 00:00:00  
  
Interestingly enough the time parameters are being "wrapped" while the same behavior is not verified for the date parameters: dates like "2020-1**3**-30T25:52:28Z" are deemed wrong and triggers the exception throwing. So here there is at least an inconsistency.  
  
Could you please help me understand why and if there is an alternative to perform validation for such kind of custom input deserialization?  
  
Best regards,  
Rodrigo

---

## Comment 1

> Username: pjtallon  
> Created at: 2021-11-26 15:03:33 UTC  
> Updated at: 2021-11-26 15:08:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/184#issuecomment-980043975  

Hello,  
I am currently working on @rbroggi's piece of code to maintain it for his previous company. I identified new edge cases to add to the stack of issues :  
- "20211123" is being accepted as a valid date, but ends up being parsed as 2021-Dec-01 (not even the right date). Dates from "20211118" up to the next edge case are impacted.  
- "20211130" is not accepted as a valid date and ends up throwing an exception.  
In these two edge case identified, the intended format is YYYYMMDD, which can be seen as valid ISO format, but can't be seen as valid against the specified format in the facet.  
  
In addition to that, I made some tests involving the previous dates, and trying to change the way the facet was being passed as it looked redundant.  
Commenting one of the other should have equivalent behaviour, but it does not.  
From the following lines :  
`ss.imbue(std::locale(std::locale::classic(), new boost::posix_time::time_facet("%Y-%m-%dT%H:%M:%SZ")));  
ss.imbue(std::locale(ss.getloc(), new boost::posix_time::time_input_facet("%Y-%m-%dT%H:%M:%SZ")));`  
  
Even keeping only one of the two previous lines was only partially making the parsing work.  
  
Either dates matching the facet format were being rejected (as well as the YYYYMMDD edge cases) OR dates matching the facet format were being accepted (as well YYYYMMDD edge cases).  
  
I would expect by default that the edge cases should be exclusive to the facet format in terms of behaviour whatever the value...  
  
The only way for us to work around the issue is to introduce an additional layer of regular-expression-based validation on the string to be converted to ptime, hence making sure only relevant formats are being accepted and parsed, which I would expect to be done in a more restrictive way by the facet natively.  
That's what I'm going to do while waiting for a status on the matter and a potential fix since correctyl formatted strings end up being parsed correctly to our knowledge.  
  
Any update on the matter would be welcome as the feature seems less stable than it should be.  
  
Best,  
Julien
