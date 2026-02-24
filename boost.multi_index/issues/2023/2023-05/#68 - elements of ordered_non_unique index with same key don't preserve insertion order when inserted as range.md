# #68 - elements of ordered_non_unique index with same key don't preserve insertion order when inserted as range [Closed]

> Username: SCFrench  
> Created at: 2023-05-19 19:16:09 UTC  
> Updated at: 2023-05-22 13:55:52 UTC  
> Closed at: 2023-05-21 09:39:50 UTC  
> Url: https://github.com/boostorg/multi_index/issues/68  

The program below as-is inserts a set of values into a multi_index_container with ordered_non_unique indices via a for loop, with each element being inserted individually. When the elements with the key "Bob" (as identified by equal_range) are iterated over, they appear in the same order as they were inserted (which can be seen by looking at the age of each "Bob"). However, if you change the `#if 1` to `#if 0` then the elements are inserted via a call to the iterator range-based overload of insert. In this case, the ages of the people named "Bob" are not displayed in the order of insertion (which I assume would be forward pass from the `begin` iterator to the `end` iterator).  
  
The following demo program can also be found here: https://gcc.godbolt.org/z/EvG4rW1ba  
  
```  
#include <iostream>  
#include <string>  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/member.hpp>  
  
using namespace boost::multi_index;  
  
struct person {  
   std::string name;  
   int age;  
};  
  
typedef multi_index_container<  
   person,  
   indexed_by<  
      ordered_non_unique<member<person, std::string, &person::name>>,  
      ordered_non_unique<member<person, int, &person::age>>  
   >   
> person_set;  
  
int main()  
{  
  std::vector<person> people = {  
      {"Bob", 32},  
      {"Charlie", 32},  
      {"Bob", 32},  
      {"Emily", 32},  
      {"Bob", 30},  
      {"Alex", 32},  
      {"Bob", 56},  
      {"Bob", 32}  
   };  
  
   person_set persons;  
  
#if 1  
   for (auto const & p : people)  
   {  
    persons.insert(p);  
   }  
#else  
  persons.insert(people.begin(), people.end());  
#endif  
  
   std::cout << "People named Bob: \n";  
   auto &name_index = persons.get<0>();  
   auto range2 = name_index.equal_range("Bob");  
   for (auto it = range2.first; it != range2.second; ++it) {  
      std::cout << it->name << ", " << it->age << "\n";  
   }  
  
   return 0;  
}  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2023-05-21 09:40:45 UTC  
> Url: https://github.com/boostorg/multi_index/issues/68#issuecomment-1556132264  

Thanks for the report! Fixed in f8143b9ff9479bc97af893c956756ec7fd4c04e8 if you'd like to confirm locally.

---

## Comment 2

> Username: SCFrench  
> Created at: 2023-05-22 13:55:52 UTC  
> Url: https://github.com/boostorg/multi_index/issues/68#issuecomment-1557266632  

Confirmed locally. Thanks for the fast response!
