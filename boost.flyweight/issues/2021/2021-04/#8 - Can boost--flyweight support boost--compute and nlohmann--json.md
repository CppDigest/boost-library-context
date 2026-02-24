# #8 - Can boost::flyweight support boost::compute and nlohmann::json? [Closed]

> Username: akeyliu  
> Created at: 2021-04-28 11:35:11 UTC  
> Updated at: 2021-05-07 11:33:26 UTC  
> Closed at: 2021-05-07 11:33:25 UTC  
> Url: https://github.com/boostorg/flyweight/issues/8  

Hello, and really thanks for your support for boost::flyweight.  
1. I'm now using boost::compute for GPU programming, and If boost::flyweight support boost:compute?  
2. I'm now using nlohmann::json for JSON process, and can boost::flyweight support nlohmann::json?  
    i.e.     
     boost::flyweight\<int\> aa = 123;  
     nlohmann::json j = aa;  
  
Thanks.  
Kevin Liu

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-04-30 11:51:40 UTC  
> Url: https://github.com/boostorg/flyweight/issues/8#issuecomment-830040755  

1: I'm not sure I understand your question: certainly you can't pass a flyweight object to OpenCL, the only admissible types are scalars and the like.  
  
2: A JSON object can only hold numbers, strings, booleans, arrays, nulls and key-value objects. So you can't store a flyweight object directly, though you can write:  
```  
 boost::flyweight<int> aa = 123;  
 nlohmann::json j = aa.get(); // get the underlying int  
```  
  
Is this what you had in mind?

---

## Comment 2

> Username: joaquintides  
> Created at: 2021-05-07 11:33:25 UTC  
> Url: https://github.com/boostorg/flyweight/issues/8#issuecomment-834291633  

Closing since there's no further feedback.
