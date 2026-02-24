# #100 - Dynamic bitset compilation broken in boost 1.90.0? [Closed]

> Username: bredelings  
> Created at: 2025-12-20 17:40:34 UTC  
> Updated at: 2025-12-22 11:22:38 UTC  
> Closed at: 2025-12-22 11:22:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/100  

The `operator-` on bit iterators fails to compile in boost v1.90. Could I be missing something here?  
  
```  
template< typename Iterator >  
BOOST_DYNAMIC_BITSET_CONSTEXPR20 std::ptrdiff_t  
                                 operator-( const bit_iterator_base< Iterator > & lhs, const bit_iterator_base< Iterator > & rhs )  
{  
    return ( lhs.m_block_iterator - rhs.m_block_iterator ) * bit_iterator_base< Iterator >::bits_per_block  
         + lhs.m_bit_index - rhs.m_bit_index;  
}  
```  
  
The error for gcc-15 looks like:  
```  
`/path/to/installed-boost-1.90.0/include/boost/dynamic_bitset/impl/dynamic_bitset.ipp:247:18: error: ‘__gnu_cxx::__normal_iterator<const long unsigned int*, std::vector<long unsigned int> > boost::bit_iterator_base<__gnu_cxx::__normal_iterator<const long unsigned int*, std::vector<long unsigned int> > >::m_block_iterator’ is protected within this context  
  247 |     return ( lhs.m_block_iterator - rhs.m_block_iterator ) * bit_iterator_base< Iterator >::bits_per_block  
      |              ~~~~^~~~~~~~~~~~~~~~  
...  
```  
  
I'm not getting errors on `operator==` and `operator<`, but it looks like they may have the same issue.  
  
Could this be fixed for boost v1.90.1?

---

## Comment 1

> Username: sehe  
> Created at: 2025-12-20 19:51:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/100#issuecomment-3678068466  

@bredelings Thanks for reporting this. I added the missing friend declaration in a PR #101   
  
The other free-function operators weren't affected as they already had the friend declarations.
