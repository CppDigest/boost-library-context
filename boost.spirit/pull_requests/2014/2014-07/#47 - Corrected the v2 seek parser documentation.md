# #47 Corrected the v2 seek parser documentation. [Merged]

> Username: vtnerd  
> Created at: 2014-07-14 03:31:23 UTC  
> Updated at: 2014-07-14 04:57:28 UTC  
> Merged at: 2014-07-14 04:57:28 UTC  
> Closed at: 2014-07-14 04:57:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/47  

This was discussed on the mailing list, the v2 seek documentation incorrectly says that the iterator position will be at the end of input on failure. It actually behaves like every other parser.  
  
There are no "failure" tests for v2 seek - I can add a test if desired. x3 already has some failure tests.

---
