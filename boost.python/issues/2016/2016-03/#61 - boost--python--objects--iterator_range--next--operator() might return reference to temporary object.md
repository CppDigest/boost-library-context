# #61 - boost::python::objects::iterator_range::next::operator() might return reference to temporary object [Open]

> Username: BorisSchaeling  
> Created at: 2016-03-09 17:35:05 UTC  
> Updated at: 2016-03-09 17:41:37 UTC  
> Url: https://github.com/boostorg/python/issues/61  

`iterator_range` in boost/python/object/iterator.hpp defines an inner class called `next` which defines `operator()` (see https://github.com/boostorg/python/blob/develop/include/boost/python/object/iterator.hpp#L44). Here is the implementation:   
  
```  
result_type  
operator()(iterator_range<NextPolicies,Iterator>& self)  
{  
  if (self.m_start == self.m_finish)  
    stop_iteration_error();  
  return *self.m_start++;  
}  
```  
  
The last line is a potential problem as a temporary object (the return value of `self.m_start++`) is dereferenced. If `result_type` is a reference (meaning `operator()` returns a reference) and the dereferenced temporary object returns a reference to something within the temporary object, you get a dangling reference.   
  
Here is how `result_type` is determined:   
  
```  
typedef boost::detail::iterator_traits<Iterator> traits_t;  
  
typedef typename mpl::if_<  
  is_reference<  
    typename traits_t::reference  
  >  
  , typename traits_t::reference  
  , typename traits_t::value_type  
>::type result_type;  
```  
  
In general this code looks good. However it implies that if a reference is returned, the reference does not refer to something in the iterator (as with the current implementation the iterator is a temporary object).   
  
Here is an attempt to fix `operator()`:   
  
```  
result_type  
operator()(iterator_range<NextPolicies,Iterator>& self)  
{  
  if (self.m_start == self.m_finish)  
    stop_iteration_error();  
  self.m_current = self.m_start++;  
  return *self.m_current;  
}  
```  
  
Here an additional member variable called `m_current` is used to turn the temporary object returned by `self.m_start++` into something which lives longer. I tested this fix, and it seems to work. However I didn't test it thoroughly (for example, I don't know when and where Boost.Python creates copies of `iterator_range`; if you have a reference to something within a member variable, the member variable better doesn't change its location in memory :).   
  
A valid question is of course whether this is a Boost.Python issue. First, I'm opening this ticket to document this issue (for others and also for myself ;). Secondly, I would be already happy if Boost.Python somehow supports me detecting this issue at compile-time (I'm fine if I get a compiler error and know I need to work around something). Thirdly, I'm working with a third-party library where iterators return references to something within iterators - it's easier for me to fix Boost.Python than all the iterators in the third-party library.

---

## Comment 1

> Username: BorisSchaeling  
> Created at: 2016-03-09 17:41:37 UTC  
> Url: https://github.com/boostorg/python/issues/61#issuecomment-194418515  

Here is a test case (rename test.txt to test.cpp):   
  
[test.txt](https://github.com/boostorg/python/files/165717/test.txt)  
  
Run this Python code for a crash:   
  
```  
import test  
v = test.vector()  
for i in v.range:  
  print(i)  
```  
  
If you change this line in test.txt:   
  
`typedef const value_type& reference;`  
  
to:   
  
`typedef const value_type reference;`  
  
it all works (`operator()` now doesn't return a reference to something in a temporary object anymore but a copy).
