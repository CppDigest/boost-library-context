# #144 Fixed bug in karma::duplicate attribute reporting [Merged]

> Username: vtnerd  
> Created at: 2015-07-31 03:33:11 UTC  
> Updated at: 2016-03-31 17:42:41 UTC  
> Merged at: 2015-11-05 02:47:28 UTC  
> Closed at: 2015-11-05 02:47:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/144  

An expression such as `generate(..., (duplicate[double_ << double_] | "foo"), 2.0)` will output "foo" even though `duplicate[double_ << double_]` should never fail. The problem is that `duplicate` adds a reference to the reported attribute type. The `is_convertible` check will fail, causing the first branch of the alternative to be ignored at compile-time.  
  
All of the tests were run on OSX with this patch - no issues. I leave it to the spirit braintrust to determine whether this could cause any issues with code in the wild.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2015-07-31 12:05:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/144#issuecomment-126669874  

LGTM, thanks!

---

## Comment 2

> Username: vtnerd  
> Created_at: 2015-11-05 02:36:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/144#issuecomment-153935041  

A gentle bump on this pull request ...

---
