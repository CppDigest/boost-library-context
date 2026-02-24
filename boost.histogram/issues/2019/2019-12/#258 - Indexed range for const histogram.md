# #258 - Indexed range for const histogram [Closed]

> Username: dakeryas  
> Created at: 2019-12-11 10:30:04 UTC  
> Updated at: 2020-01-21 17:00:49 UTC  
> Closed at: 2019-12-15 16:52:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/258  

Hello,  
  
I am trying to provide an accessor to a histogram data member in a class. To avoid having the user instantiate the `boost::histogram::indexed_range` and having to use these qualifiers (especially confusing for ROOT users). I would like to return a range over which the user can directly loop and call methods likes `.index()` and so on, simply for "printing" purposes.  
  
However, for the following const accessor:  
```   
boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>> GetHist() const;  
```  
I get with gcc 8.3.0:  
```  
../src/Analyser.cc:76:12: error: no viable conversion from returned value of type 'indexed_range<const histogram<...>>' to function return type 'indexed_range<histogram<...>>'  
    return boost::histogram::indexed(recoilHist);  
           ^~~~~~~~~~~~~~~~~~~~~~~  
~/Software/boost/include/boost/histogram/indexed.hpp:39:33: note: candidate constructor (the implicit copy constructor) not viable: no known conversion from 'boost::histogram::indexed_range<const boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default> >, boost::histogram::unlimited_storage<std::allocator<char> > > >' to 'const boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default> >, boost::histogram::unlimited_storage<std::allocator<char> > > > &' for 1st argument  
class BOOST_HISTOGRAM_NODISCARD indexed_range {  
                                ^  
~/Software/boost/include/boost/histogram/indexed.hpp:39:33: note: candidate constructor (the implicit move constructor) not viable: no known conversion from 'boost::histogram::indexed_range<const boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default> >, boost::histogram::unlimited_storage<std::allocator<char> > > >' to 'boost::histogram::indexed_range<boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::use_default, boost::use_default, boost::use_default> >, boost::histogram::unlimited_storage<std::allocator<char> > > > &&' for 1st argument  
1 error generated.  
                 ^  
  
```  
what is the conversion trick to make it work? If I remove the `const` qualifier from the accessor, the code builds (but that is obviously not the good solution).  
  
Otherwise, I am a bit puzzled but the lengthy syntax, when one needs histogram data members in a class and cannot `auto` deduction from the factories. Am I missing any predefined alias for such a simple usage? I am currently doing:  
  
```       
using Histogram = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>;  
```  
  
(does one really need to spell out `std::tuple` for a 1D version?)  
  
Thanks in advance!

---

## Comment 1

> Username: henryiii  
> Created at: 2019-12-12 03:18:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/258#issuecomment-564831864  

Either use:  
  
```cpp  
boost::histogram::indexed_range<const boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>> GetHist() const;  
```  
  
https://wandbox.org/permlink/Tz7Igy9L2YmJ4OZw  
  
Or use  
  
```cpp  
decltype(auto) GetHist() const {...}  
```  
  
https://wandbox.org/permlink/cps1fIdbYNgNG4pV  
  
The const is missing inside the indexed accessor.

---

## Comment 2

> Username: dakeryas  
> Created at: 2019-12-12 14:39:10 UTC  
> Updated at: 2020-01-21 17:00:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/258#issuecomment-565033371  

Dear Henryiii,  
  
Thank you so much! I need to study my nested template argument deductions (I was looking for some `const_indexed_range` akin to the STL `const_iterator`, but did not think about nesting the `const` qualifier)!  
  
I had tried `auto` alone, but got a   
```  
error: function 'GetHist' with deduced return type cannot be used before it is defined  
```  
I get the same with `decltype(auto)`  
```  
../Analyser.cc:16:33: error: function 'GetHist' with deduced return type cannot be used before it is defined  
    for(const auto& datum : analyser.GetHist())  
                                ^  
../include/Analyser.hh:31:20: note: 'GetHist' declared here  
    decltype(auto) GetHist() const;  
  
```  
  
 so I guess I have to spell out the full type...

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-12-13 13:35:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/258#issuecomment-565443260  

Hi @dakeryas, the signature `decltype(auto) GetHist() const` requires a function body to infer the return type. You are probably getting this error because you tried something like this:  
```  
// in header file  
class my_class {  
  decltype(auto) GetHist() const;  
};  
  
// in implementation file  
decltype(auto) my_class::GetHist() const {  
 ...  
}  
```  
If you call `GetHist` somewhere where the compiler cannot see the implementation, then it cannot deduce the return type.

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-12-13 13:46:42 UTC  
> Updated at: 2019-12-13 13:50:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/258#issuecomment-565446562  

The histogram types are long and verbose, sorry about that, it can't be helped. To avoid typing out long types, you can use `decltype` in a trailing return type signature. Examples:  
  
```c++  
// replace  
using Histogram = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<>>>;  
  
// with  
namespace bh = boost::histogram;  
using Histogram = decltype(bh::make_histogram(bh::axis::regular<>()));  
```  
Similarly:  
```c++  
#include <boost/histogram.hpp>  
  
namespace bh = boost::histogram;  
  
using Histogram = decltype(bh::make_histogram(bh::axis::regular<>()));  
  
// in header  
struct my_class {  
    Histogram my_hist; // must come before  
  
    auto GetHist() const -> decltype(bh::indexed(my_hist));  
};  
  
// in implementation file  
auto my_class::GetHist() const -> decltype(bh::indexed(my_hist)) {  
 return bh::indexed(my_hist);  
}  
```

---

## Comment 5

> Username: henryiii  
> Created at: 2019-12-13 15:34:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/258#issuecomment-565485752  

> ```python  
> using Histogram = decltype(bh::make_histogram(bh::axis::regular<>()));  
> ```  
  
I almost posted this exact example!  
  
`decltype(auto)` really just adds `&` or `&&` if they apply, while plain `auto` does not. The definition must be present. If you have a templated function, you can't compile it beforehand, so it's more often present, but if you still like to keep implementation and definition separate (which is still a good practice), then Hans' example above is probably the best way to do it and keep things readable.

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-12-15 16:52:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/258#issuecomment-565826517  

My point was not about `auto` vs. `decltype(auto)`, but about functions with trailing return type signatures. These do not need a function body and work when the implementation is not included in the header. https://www.ibm.com/developerworks/community/blogs/5894415f-be62-4bc0-81c5-3956e82276f3/entry/introduction_to_the_c_11_feature_trailing_return_types?lang=en  
  
It seems like the issue can be closed, feel free to reopen if necessary.
