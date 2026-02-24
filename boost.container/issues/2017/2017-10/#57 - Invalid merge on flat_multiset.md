# #57 - Invalid merge on flat_multiset [Closed]

> Username: edouarda  
> Created at: 2017-10-06 14:51:12 UTC  
> Updated at: 2017-10-14 10:11:20 UTC  
> Closed at: 2017-10-14 10:11:20 UTC  
> Url: https://github.com/boostorg/container/issues/57  

Hello, there seems to be a bug in a merge if there is no reallocation.  
  
Reproduce:  
  
```cpp  
using test_multiset = boost::container::flat_multiset<long>;  
  
test_multiset dst;  
  
for (size_t i = 0; i < 5; ++i)  
{  
    dst.insert(1);  
}  
  
for (size_t i = 0; i < 5; ++i)  
{  
    dst.insert(2);  
}  
  
test_multiset src;  
  
for (size_t i = 0; i < 10; ++i)  
{  
    src.insert(2);  
}  
  
dst.reserve(20);  
dst.merge(src);  
  
// failure with reserve, success without  
BOOST_CHECK(std::is_sorted(dst.cbegin(), dst.cend()));  
```

---

## Comment 1

> Username: edouarda  
> Created at: 2017-10-06 16:35:22 UTC  
> Updated at: 2017-10-06 16:35:40 UTC  
> Url: https://github.com/boostorg/container/issues/57#issuecomment-334806316  

This fixes the problem but may have side effects I am not aware of.  
  
https://github.com/boostorg/container/blob/develop/include/boost/container/vector.hpp#L2183  
  
```cpp  
   size_type const n = static_cast<size_type>(boost::container::iterator_distance(first, last));  
      size_type const s = this->size();  
      if(BOOST_LIKELY(s)){  
         size_type const c = this->capacity();  
         size_type const free_c = (c - s);  
         //Use a new buffer if current one is too small for new elements,  
         //or there is no room for position indexes  
         if(free_c < n){  
            size_type const new_size = s + n;  
            size_type new_cap = new_size;  
            pointer p = pointer();  
            p = this->m_holder.allocation_command(allocate_new, new_size, new_cap, p);  
            this->priv_merge_in_new_buffer(UniqueBool(), first, n, comp, p, new_cap);  
         }  
         else{  
            T *raw_pos = boost::movelib::iterator_to_raw_pointer(this->insert(this->cend(), first, last));  
            T *raw_beg = this->priv_raw_begin();  
            T *raw_end = this->priv_raw_end();  
  
            std::inplace_merge(raw_beg, raw_pos, raw_end, comp);  
  
       //     boost::movelib::adaptive_merge(raw_beg, raw_pos, raw_end, comp, raw_end, free_c - n);  
            if(UniqueBool::value){  
               size_type const count =  
                  static_cast<size_type>(raw_end - boost::movelib::unique(raw_beg, raw_end, boost::movelib::negate<Compare>(comp)));  
               this->priv_destroy_last_n(count);  
            }  
         }  
      }  
      else{  
         this->insert(this->cend(), n, first, last);  
      }  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-06 16:37:26 UTC  
> Url: https://github.com/boostorg/container/issues/57#issuecomment-334806888  

Preliminary troubleshooting:  
  
The bug happens in this function:  
https://github.com/boostorg/move/blob/6cb4f456d1cac72c674160c689913ac3e12e54d0/include/boost/move/algo/detail/adaptive_sort_merge.hpp#L2404  
  
`merge_bufferless` is called with the first range incorrectly sorted

---

## Comment 3

> Username: igaztanaga  
> Created at: 2017-10-07 12:38:28 UTC  
> Url: https://github.com/boostorg/container/issues/57#issuecomment-334932273  

Thanks for reporting, it was a bug in Boost.Move[1], updating Boost.Move should fix the issue. Let me know if the problem is gone.  
  
[1] https://github.com/boostorg/move/issues/15

---

## Comment 4

> Username: edouarda  
> Created at: 2017-10-07 14:59:23 UTC  
> Url: https://github.com/boostorg/container/issues/57#issuecomment-334940920  

Thank you for your timely fix.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2017-10-14 10:11:20 UTC  
> Url: https://github.com/boostorg/container/issues/57#issuecomment-336625195  

Closing the issue as fixed. Thanks for the report.
