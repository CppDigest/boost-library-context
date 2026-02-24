# #281 - Can serialization serialize a pointer of a class? [Closed]

> Username: ppzzyy11  
> Created at: 2023-04-10 10:06:28 UTC  
> Updated at: 2023-08-24 17:59:32 UTC  
> Closed at: 2023-08-24 17:59:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/281  

```  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <iostream>  
#include <sstream>  
  
std::stringstream ss;  
  
class Int{  
public:  
	int a;  
};  
  
class animal  
{  
public:  
  animal() = default;  
  animal(int legs) : legs_{legs} {}  
  int legs() const { return legs_; }  
  
public:  
  friend class boost::serialization::access;  
  
  template <typename Archive>  
  void serialize(Archive &ar, const unsigned int version) { ar & legs_; }  
  
  int legs_;  
  int *ptr = nullptr;  
  Int int_{30};  
  Int* int_ptr = nullptr;  
};  
  
void save()  
{  
  boost::archive::text_oarchive oa{ss};  
  animal *a = new animal{4};  
  a->ptr = new int(100);  
  a->int_ptr = new Int{20};  
  oa << a;  
  std::cout << std::hex << a << '\n';  
  delete a;  
}  
  
void load()  
{  
  boost::archive::text_iarchive ia{ss};  
  animal *a;  
  ia >> a;  
  std::cout << std::hex << a << '\n';  
  std::cout << std::dec << a->legs() << '\n';  
  std::cout<<(a->ptr)<<"a"<<'\n';;//ptr is nullptr....  
  std::cout<<a->int_.a<<"\t"<<(a->int_ptr?a->int_ptr->a:0)<<std::endl;;  
  delete a;  
}  
  
int main()  
{  
  save();  
  load();  
}  
  
  
```  
  
The result is below.  int* ptr and Int* are both nullptr.  
```  
0x60000067c000  
0x60000067c020  
4  
0x0a  
30	0  
```  
```

---

## Comment 1

> Username: gucoi  
> Created at: 2023-05-22 15:47:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/281#issuecomment-1557464186  

your serialize function not to serialize ptr, so deserialize not contain the ptr.  
you can try this like below  
  
template <typename Archive>  
  void serialize(Archive &ar, const unsigned int version) { ar & legs_;  ar & ptr; ...}

---

## Comment 2

> Username: robertramey  
> Created at: 2023-08-24 17:59:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/281#issuecomment-1692173541  

This is addressed in detail in the documentation for the Boost serialization library.
