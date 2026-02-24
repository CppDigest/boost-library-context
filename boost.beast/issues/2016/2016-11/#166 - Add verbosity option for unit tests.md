# #166 - Add verbosity option for unit tests [Closed]

> Username: bachase  
> Created at: 2016-11-02 17:58:05 UTC  
> Updated at: 2017-09-01 03:04:32 UTC  
> Closed at: 2017-09-01 03:04:32 UTC  
> Url: https://github.com/boostorg/beast/issues/166  

Consider adding a verbosity level to determine when `testcase` identifiers are printed.  Since `BEAST_EXPECT` reports the file/line number, those identifiers are not typically needed.  But if you have multiple pathways to `BEAST_EXPECT` for different test cases, the file/line number are not sufficient to disambiguate the test.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-01 03:04:32 UTC  
> Url: https://github.com/boostorg/beast/issues/166#issuecomment-326475749  

I believe this has been addressed - if not, please open a new issue!
