# #98 - is_permutation range version missing [Open]

> Username: allopislozano  
> Created at: 2019-12-12 13:49:39 UTC  
> Updated at: 2019-12-12 13:57:08 UTC  
> Url: https://github.com/boostorg/range/issues/98  

There is no boost::ranges::is_permutation  
  
Passing two ranges to boost::algorithm::is_permutation yields this error. The first parameter is correctly matched and the second parameter is incorrectly interpreted as a ForwardIterator without any BOOST_CONCEPT checks:  
https://godbolt.org/z/TCE8Xm  
  
It is also surprising that  an algorithm defined in boost::algorithm takes a range as a first parameter and an iterator as the second parameter  
  
Shouldn't there be a ranges version that takes two ranges? upcoming c++20 std::ranges::is_permutation takes two ranges as params:  
https://eel.is/c++draft/alg.is.permutation
