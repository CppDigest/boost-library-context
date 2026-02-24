# #799 - Investigate generating into string from user structures directly [Closed]

> Username: denzor200  
> Created at: 2022-11-04 22:44:16 UTC  
> Updated at: 2024-10-09 14:22:59 UTC  
> Closed at: 2024-10-09 14:22:59 UTC  
> Url: https://github.com/boostorg/json/issues/799  

There is only issue about parsing here: https://github.com/boostorg/json/issues/627  
But the generating is also actual question and it is also could potentially gain a lot of performance.  
  
E.g.  
```  
template<class V, class OI>  
void  
generate_into(  
    OI sink,  
    const V& v);  
  
std::vector<int> v{1, 2, 3};  
std::string s;  
boost::json::generate_into(std::back_inserter(s), v);  
std::cout << s << std::endl; // [1, 2, 3]  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-11-04 22:51:47 UTC  
> Url: https://github.com/boostorg/json/issues/799#issuecomment-1304319647  

This proof of concept already works:  
https://github.com/boostorg/json/pull/791  
  
What is missing:  
* tests  
* metaprogramming  
* fixes to the stack for alignment/space optimization  
* documentation  
* examples

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-10-09 14:22:59 UTC  
> Url: https://github.com/boostorg/json/issues/799#issuecomment-2402492729  

Implemented in #956.
