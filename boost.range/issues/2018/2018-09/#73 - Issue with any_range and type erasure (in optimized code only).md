# #73 - Issue with any_range and type erasure (in optimized code only) [Closed]

> Username: rolesen  
> Created at: 2018-09-10 08:34:37 UTC  
> Updated at: 2021-06-15 15:03:45 UTC  
> Closed at: 2020-02-13 09:36:51 UTC  
> Url: https://github.com/boostorg/range/issues/73  

When a range is transformed into a new type and then "type erased" it gives an invalid range in optimized code only. See provided examples.  
  
Using either the implicit conversion to any_range or the type erasure adaptor doesn't seem to make a difference.  
  
Permalink to wandbox:  
  
https://wandbox.org/permlink/CG2FhOBnO9gesp5w  
  
```  
  
#include <iostream>  
#include <cstdlib>  
#include <vector>  
#include <boost/range.hpp>  
#include <boost/range/adaptors.hpp>  
#include <boost/range/adaptor/type_erased.hpp>  
  
struct A {  
  virtual void hello() {}  
  int i = 0;  
};  
  
struct B : public A {  
  double d = 0.0;  
};  
  
B* cast_to_B(A* a_ptr) {  
    return static_cast<B*>(a_ptr);  
}  
  
double timesOneHalf(int in){  
    return 1.5 * in;  
}  
  
  
void example_1() {//type transformation of int to double (including calculation)  
    std::vector<int> vec{1, 2, 3};  
    auto range = boost::make_iterator_range(vec.begin(), vec.end());  
    auto range_transformed = range | boost::adaptors::transformed(timesOneHalf);  
      
    boost::any_range<double, boost::iterators::bidirectional_traversal_tag> range_typeerased = range_transformed;//WARNING: function returns address of local variable  
      
    std::cout << "transformed: " << *range_transformed.begin() << std::endl;  
    std::cout << "type erased: " << *range_typeerased.begin() << std::endl;//seg fault or prints wrong value (should be 1.5) (when compiled with optimization)  
      
    std::cout << "end of exampe 1" << std::endl;  
}  
  
void example_2() {//polymorphic type transformation example  
    std::vector<A*> vec{new B, new B, new B};  
    auto range = boost::make_iterator_range(vec.begin(), vec.end());  
    auto range_transformed = range  
    | boost::adaptors::transformed(cast_to_B);  
      
    auto range_typeerased = range_transformed | boost::adaptors::type_erased<B*, boost::iterators::bidirectional_traversal_tag, B*, std::ptrdiff_t>();//WARNING: function returns address of local variable  
      
    if (*range_transformed.begin() != *vec.begin()) {//OK (no type erasure involved yet)  
      std::cout << "ASSERT 1: should have been the same" << std::endl;  
    }   
      
    if (*range_typeerased.begin() != *vec.begin()) {//<--segmention fault or the assert fires (when compiled with optimization)  
      std::cout << "ASSERT 2: should have been the same" << std::endl;  
    }      
      
    std::cout << "end of exampe 2" << std::endl;  
}  
  
int main()  
{  
  example_1();  
  example_2();  
      
  std::cout << "Main finished!" << std::endl;  
}  
```

---

## Comment 1

> Username: rolesen  
> Created at: 2018-09-10 08:43:00 UTC  
> Updated at: 2018-09-10 08:43:43 UTC  
> Url: https://github.com/boostorg/range/issues/73#issuecomment-419834947  

I might add that it is also an issue when compiling with MSVC.

---

## Comment 2

> Username: mjendruk  
> Created at: 2019-04-02 08:25:45 UTC  
> Updated at: 2019-04-02 09:59:31 UTC  
> Url: https://github.com/boostorg/range/issues/73#issuecomment-478895797  

This issue was already posted 5 years ago in the Boost issue tracker:   
https://svn.boost.org/trac10/ticket/10493  
  
I would also like to see this fixed.  
  
Edit: The bug was probably introduced in: 79d2a66831af16f29befaa4d32461928b9431a15

---

## Comment 3

> Username: fdidoux  
> Created at: 2021-06-15 15:03:45 UTC  
> Url: https://github.com/boostorg/range/issues/73#issuecomment-861576664  

IMHO the fix didn't fully solve the issue. At least, the code provided by rolesen still doesn't compile (even with gcc 11 and boost 1.76, which as far as I can see, contains the PR) - and same with Msvc 2019 (16.9.4)
