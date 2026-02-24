# #327 - unregister derived class exception when split design files [Open]

> Username: wodadehencou  
> Created at: 2025-03-19 03:36:42 UTC  
> Updated at: 2025-03-19 03:37:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/327  

I create a simple derived class example for serialization. The difference between example in document is that I split all files.  
The whole example project is [https://github.com/wodadehencou/boost-serialize-derived](https://github.com/wodadehencou/boost-serialize-derived)  
And build can be rerun using   
```bash  
cmake -B build -S . -D CMAKE_BUILD_TYPE=Debug -D CMAKE_EXPORT_COMPILE_COMMANDS=tru  
e  
cmake --build build  
./build/src/main  
```  
  
The wrong output is   
```  
terminate called after throwing an instance of 'boost::archive::archive_exception'  
  what():  unregistered class - derived class not registered or exported  
fish: Job 1, 'build/src/main' terminated by signal SIGABRT (Abort)  
```  
  
**If I concat all files together in a single main.cpp, everything goes right.**  
  
  
I follow the instruction as in Document  
  
1. first create Base class  
    1. make `virtual ~Base()` as virtual  
    2. add `serialize` method to do nothing  
    3. add `BOOST_SERIALIZATION_ASSUME_ABSTRACT(Base)` at the bottom of Base.h  
```cpp  
  
#pragma once  
  
#include "boost/serialization/access.hpp"  
#include "boost/serialization/assume_abstract.hpp"  
  
// 基类声明  
class Base {  
public:  
  virtual void print() const = 0;  
  virtual ~Base() = default;  
  
private:  
  friend class boost::serialization::access;  
  
  template <class Archive>  
  void serialize(Archive &ar, const unsigned int version) {  
    // 基类无数据成员需要序列化  
  }  
};  
  
BOOST_SERIALIZATION_ASSUME_ABSTRACT(Base)  
```  
  
2. Then create DerivedA and DerivedB public derived from Base  
    1. public derive  
    2. add `serialize` method which calls base `ar &boost::serialization::base_object<Base>(*this);`  
    3. add `BOOST_CLASS_EXPORT_KEY(DerivedA)` in DerivedA.h  
    4. add `BOOST_CLASS_EXPORT_IMPLEMENT(DerivedA)` as the first line of DerivedA.cpp  
  
```cpp  
#pragma once  
  
#include "Base.h"  
#include "boost/serialization/base_object.hpp"  
#include "boost/serialization/export.hpp"  
  
// 派生类 A  
class DerivedA : public Base {  
public:  
  DerivedA() = default;  
  DerivedA(int x) : data(x) {}  
  virtual ~DerivedA() = default;  
  
  void print() const override;  
  
private:  
  int data = 0;  
  
  friend class boost::serialization::access;  
  
  template <class Archive>  
  void serialize(Archive &ar, const unsigned int version) {  
    ar &boost::serialization::base_object<Base>(*this);  
    ar & data;  
  }  
};  
  
BOOST_CLASS_EXPORT_KEY(DerivedA)  
```  
  
```cpp  
#include "DerivedA.h"  
#include "boost/serialization/export.hpp"  
#include <iostream>  
  
BOOST_CLASS_EXPORT_IMPLEMENT(DerivedA)  
  
void DerivedA::print() const { std::cout << "DerivedA: " << data << std::endl; }  
```  
  
3. Last, I create main.cpp and include Base.h, DerivedA.h, DerivedB.h, create a vector of std::unique_ptr<Base>. All serialization can not run successfully.   
```cpp  
#include "Base.h"  
#include "DerivedA.h"  
#include "DerivedB.h"  
#include <boost/archive/polymorphic_text_iarchive.hpp>  
#include <boost/archive/polymorphic_text_oarchive.hpp>  
#include <boost/archive/text_iarchive.hpp>  
#include <boost/archive/text_oarchive.hpp>  
#include <boost/serialization/export.hpp>  
#include <boost/serialization/unique_ptr.hpp>  
#include <boost/serialization/vector.hpp>  
#include <fstream>  
#include <vector>  
  
// BOOST_CLASS_EXPORT_IMPLEMENT(DerivedA)  
// BOOST_CLASS_EXPORT_IMPLEMENT(DerivedB)  
  
int main() {  
  
  // 创建多态对象集合  
  std::vector<std::unique_ptr<Base>> original;  
  original.emplace_back(new DerivedA(42));  
  original.emplace_back(new DerivedB("hello"));  
  original.emplace_back(new DerivedA(100));  
  
  // 序列化到文件  
  {  
    std::ofstream ofs("archive.txt");  
    boost::archive::text_oarchive oa(ofs);  
    oa << original; // 自动处理多态指针  
  }  
  
  // 从文件反序列化  
  std::vector<std::unique_ptr<Base>> restored;  
  {  
    std::ifstream ifs("archive.txt");  
    boost::archive::text_iarchive ia(ifs);  
    ia >> restored; // 自动重建正确类型  
  }  
  
  // 验证反序列化结果  
  for (const auto &ptr : restored) {  
    ptr->print();  
  }  
  
  return 0;  
}  
  
```
