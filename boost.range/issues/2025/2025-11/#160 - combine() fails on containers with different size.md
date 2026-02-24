# #160 - combine() fails on containers with different size [Open]

> Username: adan60  
> Created at: 2025-11-28 12:41:18 UTC  
> Updated at: 2025-11-28 12:41:18 UTC  
> Url: https://github.com/boostorg/range/issues/160  

The code  
  
#include \<iostream\>  
#include \<vector\>  
#include \<boost/range/combine.hpp\>  
#include \<boost/tuple/tuple.hpp\>  
int main() {  
  std::vector<int> vec1 = { 1, 2 };  
  std::vector<int> vec2 = { 5, 6, 7 };  
  const auto& zip_view = boost::range::combine(vec1, vec2);  
  std::cout << "boost::range::combine(vec1, vec2).size() = " << std::ranges::size(zip_view) << std::endl;  
  for (auto const& zipped : zip_view) {  
    int first, second;  
    boost::tie(first, second) = zipped;  
    std::cout << first << " " << second << std::endl;  
  }  
  return 0;  
}  
  
produces an output  
  
boost::range::combine(vec1, vec2).size() = 2  
1 5  
2 6  
  
and fails while access over vec1.end()
