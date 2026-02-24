# #154 - <boost/container/flat_map.hpp> use 100% CPU and behaves too slow on a specific set of data [Closed]

> Username: Manoj-red-hat  
> Created at: 2020-07-23 19:42:45 UTC  
> Updated at: 2020-10-25 15:39:10 UTC  
> Closed at: 2020-10-25 15:39:10 UTC  
> Url: https://github.com/boostorg/container/issues/154  

``` c  
#include <fstream>  
#include <string>  
#include <vector>  
#include <unordered_map>  
#include <boost/container/flat_map.hpp>  
#include <boost/version.hpp>  
#include <iostream>  
void readFileToVector(std::string file, std::vector<int> *col, int scale) {  
    std::ifstream myfile(file);  
    for (std::string line; getline(myfile, line);) {  
        col->push_back(std::stoi(line));  
    }     
}  
  
int main(){  
    std::cout << "Using Boost "  
          << BOOST_VERSION / 100000     << "."  // major version  
          << BOOST_VERSION / 100 % 1000 << "."  // minor version  
          << BOOST_VERSION % 100                // patch level  
          << std::endl;  
    std::vector<int> data;  
    boost::container::flat_map<int, int> boostHashtable;  
    std::unordered_map<int, int> stdHashtable;  
    std::cout<<"Data read initiated"<<std::endl;  
    readFileToVector("./data.txt", &data, 0);   
    std::cout<<"Hastable initiated from C++ Default"<<std::endl;  
    for(auto x : data){  
        stdHashtable[x]=x;  
    }     
    std::cout<<"Std hashmap completed with size "<<stdHashtable.size()<<std::endl;  
    std::cout<<"Hastable initiated from Boost"<<std::endl;  
    for(auto x : data){  
        boostHashtable[x]=x;  
    }     
  
}  
```  
  
Compile :  
>g++ -O3 unordered_flat_map_bug.cpp  -o unordered_flat_map_bug   
  
Output:  
> Using Boost 1.67.0  
Data read initiated  
Hastable initiated from C++ Default  
Std hashmap completed with size 9101943  
Hastable initiated from Boost **(100% CPU use and take very long time )**  
  
**Data File**   
[ Compressed data file ](https://drive.google.com/file/d/1jHEKWP0W6Uk8-BnTHPd2d2gQQy5MF8w2/view?usp=sharing)  
  
**gdb**  
> (gdb)   
boost::container::dtl::pair<int, int>::operator= (p=..., this=0x7fcf73a7ecc0) at /usr/include/boost/container/detail/pair.hpp:376  
376	   pair& operator=(BOOST_RV_REF(pair) p)  
(gdb)   
boost::container::move_backward<boost::container::dtl::pair<int, int>*, boost::container::dtl::pair<int, int>*> (r=0x7fcf73a7ecc0, l=0x7fcf73a7ecb8, f=0x7fcf72e24020)  
    at /usr/include/boost/container/detail/copy_move_algo.hpp:861  
861	   while (f != l) {  
(gdb)   
863	      *r = ::boost::move(*l);  
(gdb)   
0x0000555f2b6e3e30 in boost::container::dtl::pair<int, int>::operator= (p=..., this=<optimized out>) at /usr/include/boost/container/detail/copy_move_algo.hpp:859  
859	   move_backward(I f, I l, F r)  
(gdb)   
boost::container::move_backward<boost::container::dtl::pair<int, int>*, boost::container::dtl::pair<int, int>*> (r=0x7fcf73a7ecb8, l=0x7fcf73a7ecb0, f=0x7fcf72e24020)  
    at /usr/include/boost/container/detail/copy_move_algo.hpp:863  
863	      *r = ::boost::move(*l);  
(gdb)   
boost::container::dtl::pair<int, int>::operator= (p=..., this=0x7fcf73a7ecb8) at /usr/include/boost/container/detail/pair.hpp:376  
376	   pair& operator=(BOOST_RV_REF(pair) p)  
(gdb)   
boost::container::move_backward<boost::container::dtl::pair<int, int>*, boost::container::dtl::pair<int, int>*> (r=0x7fcf73a7ecb8, l=0x7fcf73a7ecb0, f=0x7fcf72e24020)  
    at /usr/include/boost/container/detail/copy_move_algo.hpp:861  
861	   while (f != l) {  
(gdb)   
863	      *r = ::boost::move(*l);  
(gdb)   
0x0000555f2b6e3e30 in boost::container::dtl::pair<int, int>::operator= (p=..., this=<optimized out>) at /usr/include/boost/container/detail/copy_move_algo.hpp:859  
859	   move_backward(I f, I l, F r)  
(gdb)   
boost::container::move_backward<boost::container::dtl::pair<int, int>*, boost::container::dtl::pair<int, int>*> (r=0x7fcf73a7ecb0, l=0x7fcf73a7eca8, f=0x7fcf72e24020)  
    at /usr/include/boost/container/detail/copy_move_algo.hpp:863  
863	      *r = ::boost::move(*l);  
(gdb)   
boost::container::dtl::pair<int, int>::operator= (p=..., this=0x7fcf73a7ecb0) at /usr/include/boost/container/detail/pair.hpp:376  
376	   pair& operator=(BOOST_RV_REF(pair) p)  
......................... **KEEP ON REPEATING**

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-10-25 15:39:10 UTC  
> Url: https://github.com/boostorg/container/issues/154#issuecomment-716167533  

Running your example with the latest (1.74) Boost version shows that when the number of elements passes a limit, the data structure does not fit in cache and the reallocations/copies start doing a lot of harm. flat_map is NOT a hashtable, it's an ordered sequence, so this is not really a bug, but expected behaviour explained in [flat_(multi)map/set associative containers](https://www.boost.org/doc/libs/1_58_0/doc/html/container/non_standard_containers.html#container.non_standard_containers.flat_xxx).  
  
flat_map has slow insertions because it needs to insert the new element and move the rest. This means that this initialization complexity is quadratic,.  
  
To improve this, you can first copy all data to the sequence_type and the adopt it, which makes the insertion really fast:  
  
```  
#include <fstream>  
#include <string>  
#include <boost/container/flat_map.hpp>  
#include <boost/move/utility_core.hpp>  
#include <boost/version.hpp>  
#include <iostream>  
  
template<class V>  
void readFileToSequence(std::string file, V *col, int scale) {  
    std::ifstream myfile(file);  
    for (std::string line; getline(myfile, line);) {  
        int i = std::stoi(line);  
        col->push_back({i, i});  
    }     
}  
  
int main(){  
    std::cout << "Using Boost "  
          << BOOST_VERSION / 100000     << "."  // major version  
          << BOOST_VERSION / 100 % 1000 << "."  // minor version  
          << BOOST_VERSION % 100                // patch level  
          << std::endl;  
    typedef boost::container::flat_map<int, int> flat_map_t;  
    flat_map_t flatMap;  
    typename flat_map_t::sequence_type data;  
  
    std::cout<<"Data read initiated"<<std::endl;  
    readFileToSequence("./data.txt", &data, 0);   
  
   std::cout<<"flatMap will adopt the sequence"<<std::endl;  
   flatMap.adopt_sequence(boost::move(data));  
   std::cout << "Finished! Size:" << flatMap.size() << '\n';  
}  
```  
  
which prints:  
  
> Using Boost 1.74.0  
> Data read initiated  
> flatMap will adopt the sequence  
> Finished! Size:9101943
