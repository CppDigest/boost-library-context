# #261 [issue] add default cnstr to reverse_partial_t [Closed]

> Username: ricejasonf  
> Created at: 2016-03-08 20:26:42 UTC  
> Updated at: 2016-06-14 16:59:28 UTC  
> Closed at: 2016-06-14 16:59:28 UTC  
> Url: https://github.com/boostorg/hana/pull/261  

This PR is to help with #260.  
  
Note that I copied the comment from the implementation of `partial_t`, though I wouldn't expect a default constructor to be implicitly provided in the presence of a user-declared constructor. I'm not really sure what it is saying.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-06-14 14:54:04 UTC  
> Url: https://github.com/boostorg/hana/pull/261#issuecomment-225907769  

@ricejasonf I added my own test case instead of yours, which was more focused on metafunctions than default-constructibility.

---
