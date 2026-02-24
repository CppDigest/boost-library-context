# #201 - About the function declaration of boost::match_results::length(),str(), operator[] [Closed]

> Username: ywx  
> Created at: 2023-12-31 14:15:14 UTC  
> Updated at: 2024-03-25 17:32:40 UTC  
> Closed at: 2024-03-25 17:02:20 UTC  
> Url: https://github.com/boostorg/regex/issues/201  

### About the function declaration of boost::match_results::length(),str(), operator[]  
  
  
in boost/regex/v4/match_results.hpp  
in boost/regex/v5/match_results.hpp  
  
```c++  
template <class BidiIterator, class Allocator>  
class match_results  
{  
  
...  
  
   difference_type length(int sub = 0) const  
   {  
      if(m_is_singular)  
         raise_logic_error();  
      sub += 2;  
      if((sub < (int)m_subs.size()) && (sub > 0))  
         return m_subs[sub].length();  
      return 0;  
   }  
  
...  
  
   difference_type position(size_type sub = 0) const  
   {  
      if(m_is_singular)  
         raise_logic_error();  
      sub += 2;  
      if(sub < m_subs.size())  
      {  
         const sub_match<BidiIterator>& s = m_subs[sub];  
         if(s.matched || (sub == 2))  
         {  
            return std::distance((BidiIterator)(m_base), (BidiIterator)(s.first));  
         }  
      }  
      return ~static_cast<difference_type>(0);  
   }  
  
  
...  
  
   string_type str(int sub = 0) const  
   {  
      if(m_is_singular)  
         raise_logic_error();  
      sub += 2;  
      string_type result;  
      if(sub < (int)m_subs.size() && (sub > 0))  
      {  
         const sub_match<BidiIterator>& s = m_subs[sub];  
         if(s.matched)  
         {  
            result = s.str();  
         }  
      }  
      return result;  
   }  
  
...  
  
   const_reference operator[](int sub) const  
   {  
      if(m_is_singular && m_subs.empty())  
         raise_logic_error();  
      sub += 2;  
      if(sub < (int)m_subs.size() && (sub >= 0))  
      {  
         return m_subs[sub];  
      }  
      return m_null;  
   }  
  
...  
  
}  
  
```  
in std::regex::match_results  
  
```c++  
   difference_type length(size_type sub = 0) const  
   difference_type position(size_type sub = 0) const  
   string_type str(size_type sub = 0) const  
   const_reference operator[](size_type sub) const  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-25 17:02:20 UTC  
> Url: https://github.com/boostorg/regex/issues/201#issuecomment-2018481485  

As you may be aware, this library pre-dates `std::regex` and we have always supported negative indexes which have special meaning for these functions.  Removing that would not only break a lot of existing code, but also remove significant and useful functionality.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-03-25 17:32:39 UTC  
> Url: https://github.com/boostorg/regex/issues/201#issuecomment-2018537942  

But see also https://github.com/boostorg/regex/pull/208.
