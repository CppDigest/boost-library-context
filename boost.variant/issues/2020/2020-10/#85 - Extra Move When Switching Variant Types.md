# #85 - Extra Move When Switching Variant Types [Closed]

> Username: smalls12  
> Created at: 2020-10-18 18:57:15 UTC  
> Updated at: 2020-11-18 19:12:06 UTC  
> Closed at: 2020-11-18 19:04:15 UTC  
> Url: https://github.com/boostorg/variant/issues/85  

Hello,  
  
I've been dabbling with variants the past couple days and started to venture into some differences between boost::variant and std::variant.  
  
I made a little example to switch between the two types( std and boost ) and it appears to me that there is an extra move when switching between the internal types within the variant when using boost.  
  
ie. variant<A, B>  
switching from A to B for instance  
  
Here is the example:  
[https://godbolt.org/z/eEqsfG](url)  
  
The extra move is not present using std::variant.  
  
The example simply creates a variant and then switches between the types.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-11-18 19:04:15 UTC  
> Url: https://github.com/boostorg/variant/issues/85#issuecomment-729890606  

This is not a bug, but rather a difference in implementation. Approach with two moves allows you to do member self assignments:  
```  
boost::variant<int, std::vector<int>> v = std::vector<int>{1,2,3,4,5};  
v = boost::get<std::vector<int>>(v).back(); // crashes with std::variant  
```  
  
This is a quite usefeull feature :)

---

## Comment 2

> Username: smalls12  
> Created at: 2020-11-18 19:12:06 UTC  
> Url: https://github.com/boostorg/variant/issues/85#issuecomment-729894665  

Ah ok, thanks for clarifying :)
