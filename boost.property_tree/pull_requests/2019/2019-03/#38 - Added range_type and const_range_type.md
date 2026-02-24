# #38 Added range_type and const_range_type. [Closed]

> Username: dmilos  
> Created at: 2019-03-07 10:51:42 UTC  
> Updated at: 2023-01-24 15:02:47 UTC  
> Closed at: 2021-05-29 18:06:32 UTC  
> Url: https://github.com/boostorg/property_tree/pull/38  

Added range_type and const_range_type.  
- typedef std::pair<assoc_iterator, assoc_iterator> range_type;  
- typedef std::pair<const_assoc_iterator, const_assoc_iterator> const_range_type;

---

## Comment 1

> Username: madmongo1  
> Created_at: 2021-05-29 18:06:32 UTC  
> Url: https://github.com/boostorg/property_tree/pull/38#issuecomment-850874805  

Interesting idea.   
These type names don't have any analogue in the standard.   
Would it be possible to create a justification via an Issue?  
A PR seems a little premature at this stage.

---

## Comment 2

> Username: dmilos  
> Created_at: 2023-01-24 15:02:03 UTC  
> Updated_at: 2023-01-24 15:02:47 UTC  
> Url: https://github.com/boostorg/property_tree/pull/38#issuecomment-1402090270  

>>These type names don't have any analogue in the standard.  
  
Before something enter to standard that should not have any analogue in standard. If have some analogue then it will be redundancy ad pointless to enter.  _basic_ptree_ also have no analogue in standard too.  
  
>>Would it be possible to create a justification via an Issue?  
  
Answer is same as for question: What is the purpose of _typedef_ or _using_.

---
