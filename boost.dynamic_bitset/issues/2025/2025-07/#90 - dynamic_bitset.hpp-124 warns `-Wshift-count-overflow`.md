# #90 - dynamic_bitset.hpp:124 warns `-Wshift-count-overflow` [Closed]

> Username: nega0  
> Created at: 2025-07-08 15:07:59 UTC  
> Updated at: 2025-07-31 14:32:01 UTC  
> Closed at: 2025-07-31 14:32:01 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/90  

Boost v1.88 with Xcode 15.2 (Apple clang 15, llvm 16) on macOS Ventura (13.7, x86_64) warns:  
  
```  
In file included from /usr/local/include/boost/dynamic_bitset.hpp:15:  
In file included from /usr/local/include/boost/dynamic_bitset/dynamic_bitset.hpp:45:  
/usr/local/include/boost/dynamic_bitset/detail/dynamic_bitset.hpp:124:19: error: shift count >= width of type [-Werror,-Wshift-count-overflow]  
            value >>= table_width;  
                  ^   ~~~~~~~~~~~  
/usr/local/include/boost/dynamic_bitset/detail/dynamic_bitset.hpp:191:21: note: in instantiation of function template specialization 'boost::detail::dynamic_bitset_impl::popcount<unsigned char>' requested here  
            num1 += popcount<ValueType>(*first);  
                    ^  
/usr/local/include/boost/dynamic_bitset/detail/dynamic_bitset.hpp:215:20: note: in instantiation of function template specialization 'boost::detail::dynamic_bitset_impl::do_count<const unsigned char *, unsigned char>' requested here  
            return do_count(p, length, static_cast<byte_type>(0u),  
                   ^  
/usr/local/include/boost/dynamic_bitset/dynamic_bitset.hpp:1246:12: note: in instantiation of function template specialization 'boost::detail::dynamic_bitset_impl::do_count<std::__wrap_iter<const unsigned char *>>' requested here  
    return do_count(m_bits.begin(), num_blocks(), Block(0),  
           ^  
foo.cxx:151:47: note: in instantiation of member function 'boost::dynamic_bitset<unsigned char>::count' requested here  
```  
  
This also shows up as a Boost regression at (clang 15, Linux, aarch64):  
https://regression.boost.io/develop/output/teeks99-06-dc15-2b-64onaarch64-dynamic_bitset-clang-linux-15~c++2b-warnings.html  
  
I don't recall seeing this on other os/compiler combos. The Boost regression board would have more combos than I'm running anyway.

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-07-14 09:13:30 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/90#issuecomment-3068569651  

I see that's a `dynamic_bitset< unsigned char >`, and the blocks are shifted by 8, so Clang is probably right. But the new implementation of `count()` doesn't use any of the above code, so this is no longer relevant. Thanks for reporting, though!
