# #13 - Travis tests broken by recent commit [Closed]

> Username: pdimov  
> Created at: 2017-12-04 23:39:41 UTC  
> Updated at: 2017-12-10 13:49:34 UTC  
> Closed at: 2017-12-10 13:49:34 UTC  
> Url: https://github.com/boostorg/icl/issues/13  

Hi Joachim,  
  
It appears that https://github.com/boostorg/icl/commit/97ddf972407d4631879527d8e6f1478b0697e93f has broken the tests on all versions of g++ and clang++ on Travis (which I recently activated), as can be seen here:  
  
https://travis-ci.org/boostorg/icl  
  
f.ex.  
  
https://travis-ci.org/boostorg/icl/builds/311052699  
  
You should be getting notification e-mails from Travis for these failures in principle, but it seems that you do not. So I'm letting you know.
