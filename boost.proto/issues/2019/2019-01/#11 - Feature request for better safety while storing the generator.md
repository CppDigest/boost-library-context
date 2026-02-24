# #11 - Feature request for better safety while storing the generator [Open]

> Username: apolukhin  
> Created at: 2019-01-10 20:00:32 UTC  
> Updated at: 2019-01-10 20:00:32 UTC  
> Url: https://github.com/boostorg/proto/issues/11  

This issue is related to the https://github.com/boostorg/spirit/issues/436  
  
The problem is that it is very simple to misuse the Spirit (or Proto) by storing a result that holds references to temporaries. @Kojoley noted that there may be a way to static assert such cases or store the input by value if the value is an rvalue.  
  
Test case and some discussion available at https://github.com/boostorg/spirit/issues/436
