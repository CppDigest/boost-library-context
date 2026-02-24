# #68 - PFR does not work with non movable types [Closed]

> Username: apolukhin  
> Created at: 2021-02-03 14:27:30 UTC  
> Updated at: 2021-04-30 09:24:41 UTC  
> Closed at: 2021-04-30 09:24:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/68  



---

## Comment 1

> Username: denzor200  
> Created at: 2021-02-13 19:46:57 UTC  
> Updated at: 2021-02-13 19:47:59 UTC  
> Url: https://github.com/boostorg/pfr/issues/68#issuecomment-778668620  

What do you mean by "non movable types"?  
In my artificial example it's right  
  
```  
struct A  
{  
    A() = default;  
    std::string type;  
    std::string value;  
  
    A(const A&) = default;  
    A& operator=(const A&) = default;  
  
    A(A&&) = delete;  
    A& operator=(A&&) = delete;  
};  
  
A a;  
A b = a;  
// A c = std::move(a); ///< error: use of deleted function ‘A::A(A&&)’  
  
boost::pfr::get<0>(a) = "Some type";  
boost::pfr::get<1>(a) = "Hello world!!";  
  
std::cout << boost::pfr::get<0>(a) << " : " << boost::pfr::get<1>(a) << std::endl; ///< outputs "Some type : Hello world!!"  
```  
Or are you not talking about this?

---

## Comment 2

> Username: apolukhin  
> Created at: 2021-04-09 07:07:15 UTC  
> Url: https://github.com/boostorg/pfr/issues/68#issuecomment-816464398  

The copy constructor should be also deleted https://github.com/boostorg/pfr/blob/develop/test/run/non_movable.cpp  
  
The issue was fixed in https://github.com/boostorg/pfr/commit/638fefedd09256b928f05e2aaf11f0a941df4b22#diff-99686eb583b72a779722f606ed8a9a5322d3a34eaf637e2f8ae0ed1c6f7eac11 and https://github.com/boostorg/pfr/commit/f33c357af6f74c319a0277993e16141ac40b00ff  
  
Will be merged to the master after the 1.76 release

---

## Comment 3

> Username: apolukhin  
> Created at: 2021-04-30 09:24:41 UTC  
> Url: https://github.com/boostorg/pfr/issues/68#issuecomment-829965662  

Merged to master
