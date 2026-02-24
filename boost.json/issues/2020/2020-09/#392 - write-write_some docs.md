# #392 - write/write_some docs [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:13:00 UTC  
> Updated at: 2020-10-04 01:37:08 UTC  
> Closed at: 2020-10-04 01:37:08 UTC  
> Url: https://github.com/boostorg/json/issues/392  

I found it rather difficult to figure out the difference in the behavior of parser::write and parser::write_some just from reading the documentation. Either reference documentation should clearly state what the difference is. I had to open both pages side by side to actually spot the wording difference. For example, I think that the documentation of write_some should clearly state that it's intended use case is for parsing multiple JSON values from a byte stream presented through one or more buffers whereas write is intended for parsing one JSON value from one or more buffers. At least that's what I concluded from the documentation after some time.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-04 01:37:08 UTC  
> Url: https://github.com/boostorg/json/issues/392#issuecomment-703186448  

This should be good now
