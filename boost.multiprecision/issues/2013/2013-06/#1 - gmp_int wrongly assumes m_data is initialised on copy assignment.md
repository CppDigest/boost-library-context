# #1 - gmp_int wrongly assumes m_data is initialised on copy assignment [Closed]

> Username: andreabedini  
> Created at: 2013-06-06 23:35:59 UTC  
> Updated at: 2013-06-11 01:38:47 UTC  
> Closed at: 2013-06-11 01:38:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/1  

... but m_data might be left uninitialised after a move and gmp will segfaults. Here is a test case.  
  
``` c++  
#include <boost/multiprecision/gmp.hpp>   
#include <vector>  
  
using boost::multiprecision::mpz_int;  
  
int main(int argc, char const *argv[])  
{  
    std::vector<mpz_int> elements_(10, 123);  
    elements_.resize(1); // this leaves garbage after the end  
    mpz_int e = 321;  
    elements_.insert(elements_.begin(), e);  
    return 0;  
}  
```  
  
Compiled with libc++  
  
```  
clang++ -g -O0 -std=c++11 -stdlib=libc++ -I$BOOST_ROOT -lgmp test.cpp -o test  
```  
  
will segfaults on the insert.  
  
```  
* thread #1: tid = 0x1c03, 0x0000000100051ae4 libgmp.10.dylib`__gmpn_copyi + 548, stop reason = EXC_BAD_ACCESS (code=1, address=0x0)  
    frame #0: 0x0000000100051ae4 libgmp.10.dylib`__gmpn_copyi + 548  
    frame #1: 0x0000000100020e1b libgmp.10.dylib`__gmpz_set + 55  
    frame #2: 0x00000001000033f7 test`boost::multiprecision::backends::gmp_int::operator=(this=0x00000001001039e0, o=0x00007fff5fbff550) + 55 at gmp.hpp:1019  
    frame #3: 0x00000001000025ef test`boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (this=0x00000001001039e0, e=0x00007fff5fbff550)1>::operator=(boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (boost::multiprecision::expression_template_option)1> const&) + 47 at number.hpp:148  
    frame #4: 0x0000000100001859 test`std::__1::vector<boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (this=0x00007fff5fbff588, __position=(null), __x=0x00007fff5fbff550)1>, std::__1::allocator<boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (boost::multiprecision::expression_template_option)1> > >::insert(std::__1::__wrap_iter<boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (boost::multiprecision::expression_template_option)1> const*>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (boost::multiprecision::expression_template_option)1> const&) + 841 at vector:1609  
    frame #5: 0x0000000100001244 test`main(argc=1, argv=0x00007fff5fbff650) + 340 at test.cpp:11  
    frame #6: 0x00007fff918ff7e1 libdyld.dylib`start + 1  
```  
  
The issue is in `gmp_int& operator = (const gmp_int& o)` which calls `mpz_set(m_data, o.m_data)` wrongly assuming `m_data` is a coherent state, which is false after a move since `gmp_int(gmp_int&& o)` sets `o.m_data[0]._mp_d = 0`.  
  
The problem seems not to appear with libstdc++ (at least my version), perhaps because std::vector does a copy rather than a move to make space for the inserted elements.  
  
I reckon the same problem might be presents in other places in gmp.hpp. Wouldn't also be better to replace `o.m_data[0]._mp_d = 0` with a proper deinitialisation ?

---

## Comment 1

> Username: andreabedini  
> Created at: 2013-06-11 01:38:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/1#issuecomment-19236552  

fixed in 36eb9001cd57c708952d808fd1a0e88ce68b5a73
