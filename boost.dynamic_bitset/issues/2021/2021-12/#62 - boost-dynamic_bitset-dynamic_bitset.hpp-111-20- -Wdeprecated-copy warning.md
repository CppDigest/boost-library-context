# #62 - boost/dynamic_bitset/dynamic_bitset.hpp:111:20: -Wdeprecated-copy warning [Closed]

> Username: Kojoley  
> Created at: 2021-12-15 01:03:00 UTC  
> Updated at: 2025-07-14 15:22:04 UTC  
> Closed at: 2025-07-14 15:22:04 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/62  

```  
clang-linux.compile.c++ ../../../../bin.v2/libs/spirit/test/qi/qi_range_run.test/clang-linux-12/release/cxxstd-11-iso/stdlib-libc++/threading-multi/visibility-hidden/range_run.o  
In file included from range_run.cpp:12:  
In file included from ../../../../boost/dynamic_bitset.hpp:15:  
../../../../boost/dynamic_bitset/dynamic_bitset.hpp:111:20: error: definition of implicit copy constructor for 'reference' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        reference& operator=(const reference& rhs) { do_assign(rhs); return *this; } // for b[i] = b[j]  
                   ^  
../../../../boost/dynamic_bitset/dynamic_bitset.hpp:306:16: note: in implicit copy constructor for 'boost::dynamic_bitset<unsigned int>::reference' first required here  
        return reference(m_bits[block_index(pos)], bit_index(pos));  
               ^  
range_run.cpp:67:13: note: in instantiation of member function 'boost::dynamic_bitset<unsigned int>::operator[]' requested here  
            bset[j-const_min] = set;  
            ^  
range_run.cpp:193:9: note: in instantiation of function template specialization 'acid_test<char>' requested here  
        acid_test<char>();  
        ^  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2024-11-26 21:00:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/62#issuecomment-2501925844  

Is this still an issue?

---

## Comment 2

> Username: gennaroprota  
> Created at: 2025-07-07 10:50:22 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/62#issuecomment-3044465716  

Fixed, thanks.
