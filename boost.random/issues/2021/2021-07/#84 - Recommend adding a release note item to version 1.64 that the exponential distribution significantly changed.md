# #84 - Recommend adding a release note item to version 1.64 that the exponential distribution significantly changed [Open]

> Username: fdinel  
> Created at: 2021-07-05 14:51:10 UTC  
> Updated at: 2021-07-05 14:51:10 UTC  
> Url: https://github.com/boostorg/random/issues/84  

Hi,  
  
We are using the exponential distribution from this library in our simulation software and recently upgraded from boost 1.60 to 1.76. I noticed significant result changes in our unit tests and investigated the difference. I just found out that version 1.64 of the exponential distribution code has been significantly modified and that it causes the difference in our test results.  
  
Long story short, I suggest that you add a line in the release notes of release 1.64 that the exponential distribution changed and could break results from previous versions.  
  
Kind regards
