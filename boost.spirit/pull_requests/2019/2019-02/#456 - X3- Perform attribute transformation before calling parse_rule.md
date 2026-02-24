# #456 X3: Perform attribute transformation before calling parse_rule [Merged]

> Username: Kojoley  
> Created at: 2019-02-08 11:57:18 UTC  
> Updated at: 2019-02-08 18:55:18 UTC  
> Merged at: 2019-02-08 18:55:12 UTC  
> Closed at: 2019-02-08 18:55:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/456  

This will deal with linking problems when rule attribute type is not the same  
as the actual attribute type. It will not harm anyone as the transformation  
after parse_rule will just pass-through the attribute because it will be the  
same there. It will also do not alter rule_definition parsing so other usages  
are still valid.  
  
Refs #454, supersedes #347.

---
