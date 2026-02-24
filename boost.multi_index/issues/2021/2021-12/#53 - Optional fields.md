# #53 - [Feature request] Optional fields [Closed]

> Username: YaZasnyal  
> Created at: 2021-12-22 16:55:17 UTC  
> Updated at: 2022-01-11 20:52:30 UTC  
> Closed at: 2022-01-11 20:52:30 UTC  
> Url: https://github.com/boostorg/multi_index/issues/53  

Hello, I really like your library but there is one thing that I miss while using it. Databases usually can create indexes of optional unique fields and this can be very useful. As I can see there is no way to model this behavior using multi_index container because there can be only one empty optional in the container. Maybe I am just missing something from documentation.  
  
I am thinking of is as an additional index type that will add values to the index only if optional holds a value. But such a solution will require changes in other types of the library because if used incorrectly (single optional index) it can leak objects because there won't be any references in indexes.

---

## Comment 1

> Username: joaquintides  
> Created at: 2021-12-24 09:35:08 UTC  
> Updated at: 2021-12-24 10:22:12 UTC  
> Url: https://github.com/boostorg/multi_index/issues/53#issuecomment-1000752245  

Hi Artem,  
  
The idea of *conditional indices*, i.e. indices taking only a subset of the elements in the container, has been floating around for a number of years. A possible interface for those would be:  
````  
using container=boost::multi_index_container<  
  element,  
  indexed_by<  
    conditional<  
      key<&element::x>, // accept into the index if element.x --> true  
      ordered_unique<key<&element::x>> // underlying index (for accepted elements)  
    >,  
    ... // rest of indices  
  >  
>;  
````   
and I know of at least one project where they have implemented this by hacking into Boost.MultiIndex code.  
  
The problem with conditional indices, however, is that they don't play so nicely in a general setting:  
* Projecting an iterator to a conditional index would be sort of UB when the element is not within the target index.  
* A  `multi_index_container` composed exclusively of conditional indices might end up holding elements which are not accessible through any of the indices.  
  
For these reasons I'm very hesitant to provide this as an official feature of the library.  
  
A poor-man solution would be to use some sort of `multi_optional<T>` class that behaves like `std::optional<T>` except that null values compare differently: this would allow for multiple null elements to be stored in a unique index (unlike with `std::optional`, as you correctly point out). A sketch of this idea follows:  
````  
#include <memory>  
#include <optional>  
#include <variant>  
  
template<typename T>  
class multi_optional  
{  
public:  
  constexpr multi_optional():impl{std::make_shared<int>(0)}{}  
  constexpr multi_optional(std::nullopt_t):multi_optional{}{}  
  template<typename U=T>  
  constexpr multi_optional(U&& value):impl{std::forward<U>(value)}{}  
    
  constexpr const T* operator->()const noexcept{return std::addressof(**this);}  
  constexpr T* operator->()noexcept{return std::addressof(**this);}  
  constexpr const T& operator*()const noexcept{return std::get<1>(impl);}  
  constexpr T& operator*() noexcept{return std::get<1>(impl);}  
    
  constexpr explicit operator bool()const noexcept{return has_value();}  
  constexpr bool has_value()const noexcept{return impl.index()==1;}  
  
  constexpr T& value()  
  {  
     if(has_value())return **this;  
     else throw std::bad_optional_access{};  
  }  
    
  constexpr const T& value()const  
  {  
     if(has_value())return **this;  
     else throw std::bad_optional_access{};  
  }  
    
  template<typename U>  
  constexpr T value_or(U&& default_value)const  
  {  
     return has_value()?**this:static_cast<T>(std::forward<U>(default_value));  
  }  
    
  constexpr void swap(multi_optional& other){impl.swap(other.impl);}  
  constexpr void reset(){impl=multi_optional{};}  
    
private:  
  template<typename U,typename V>  
  friend constexpr bool operator==  
    (const multi_optional<U>& lhs,const multi_optional<V>& rhs)  
  {  
    return lhs.impl==rhs.impl;  
  }  
  
  template<typename U,typename V>  
  friend constexpr bool operator!=  
    (const multi_optional<U>& lhs,const multi_optional<V>& rhs)  
  {  
    return lhs.impl!=rhs.impl;  
  }  
      
  template<typename U,typename V>  
  friend constexpr bool operator<  
    (const multi_optional<U>& lhs,const multi_optional<V>& rhs)  
  {  
    return lhs.impl<rhs.impl;  
  }  
    
  template<typename U,typename V>  
  friend constexpr bool operator<=  
    (const multi_optional<U>& lhs,const multi_optional<V>& rhs)  
  {  
    return lhs.impl<=rhs.impl;  
  }  
  
  template<typename U,typename V>  
  friend constexpr bool operator>  
    (const multi_optional<U>& lhs,const multi_optional<V>& rhs)  
  {  
    return lhs.impl>rhs.impl;  
  }  
  
  template<typename U,typename V>  
  friend constexpr bool operator>=  
    (const multi_optional<U>& lhs,const multi_optional<V>& rhs)  
  {  
    return lhs.impl>=rhs.impl;  
  }  
    
  std::variant<  
    std::shared_ptr<int>,  
    T  
  > impl;  
};  
  
template<typename T>  
constexpr void swap(multi_optional<T>& lhs,multi_optional<T>& rhs)  
{  
  lhs.swap(rhs);  
}  
  
namespace std{  
   
template<typename T>  
struct less<multi_optional<T>>  
{  
  constexpr bool operator()  
    (const multi_optional<T>& lhs,const multi_optional<T>& rhs)const  
  {  
    return lhs<rhs;  
  }  
    
  constexpr bool operator()  
    (const multi_optional<T>& lhs,const std::nullopt_t&)const  
  {  
    return false;  
  }  
    
  constexpr bool operator()  
    (const std::nullopt_t&,const multi_optional<T>& rhs)const  
  {  
    return rhs.has_value();  
  }  
};  
  
} // namespace std  
  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/ordered_index.hpp>  
#include <boost/multi_index/identity.hpp>  
#include <iostream>  
  
template<typename T>  
std::ostream& operator<<(std::ostream& os,const multi_optional<T>& x)  
{  
  if(x)return os<<*x;  
  else return os<<"nullopt";  
}  
  
int main()  
{  
  using namespace boost::multi_index;  
  using container=multi_index_container<  
    multi_optional<int>,  
    indexed_by<  
      ordered_unique<identity<multi_optional<int>>>  
    >  
  >;  
    
  container c;  
  for(int i=0;i<4;++i){  
    c.insert(std::nullopt);  
    c.insert(i);  
  }  
    
  for(const auto& x:c)std::cout<<x<<" ";  
  std::cout<<"\n";  
    
  // just the elements with a value  
  for(auto first=c.upper_bound(std::nullopt),last=c.end();first!=last;++first){  
    std::cout<<*first<<" ";  
  }  
  std::cout<<"\n";  
}  
````  
**Output**  
````  
nullopt nullopt nullopt nullopt 0 1 2 3   
0 1 2 3   
````

---

## Comment 2

> Username: YaZasnyal  
> Created at: 2022-01-11 20:52:30 UTC  
> Url: https://github.com/boostorg/multi_index/issues/53#issuecomment-1010350294  

Thank you very much for your response. I agree than adding optional values will have consequences especially if used incorrectly. I got the idea of your solution and will try it next time I will need this.
