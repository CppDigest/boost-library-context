# #402 - parser::release doc [Closed]

> Username: vinniefalco  
> Created at: 2020-09-22 17:41:22 UTC  
> Updated at: 2020-09-24 18:19:27 UTC  
> Closed at: 2020-09-24 18:19:27 UTC  
> Url: https://github.com/boostorg/json/issues/402  

parser::release" says "UB if the parser is not done" but "If ! this->done(), an exception is thrown.", which seems contradictory. I'd remove the UB here, maybe use an EC overload if exceptions should be avoided
