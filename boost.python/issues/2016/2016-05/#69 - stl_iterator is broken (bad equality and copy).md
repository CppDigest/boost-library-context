# #69 - stl_iterator is broken (bad equality and copy) [Closed]

> Username: ghost  
> Created at: 2016-05-27 00:53:25 UTC  
> Updated at: 2018-04-15 18:14:57 UTC  
> Closed at: 2018-04-15 18:14:57 UTC  
> Url: https://github.com/boostorg/python/issues/69  

The following asserts, which should be valid, fail.   
  
```  
void broken_equality(boost::python::numeric::array& data) {  
    using boost::python;  
  
    stl_input_iterator<int> begin(data);  
    stl_input_iterator<int> begin_other(data);  
    boost::python::stl_input_iterator<int> end;  
  
    assert(begin == begin_other);  
  
    ++begin;  
  
    assert(begin != begin_other); // fails: evaluates to true when should be false  
}  
```  
  
The equality check in the below function is incorrect, it only checks that both iterators are pointing to a non-null object.  
  
```  
bool stl_input_iterator_impl::equal(stl_input_iterator_impl const &that) const  
{  
    return !this->ob_ == !that.ob_;  
}  
```  
  
Next, copying iterators copies the internal state in a way that is **not** independent of its source, i.e.  
  
```  
stl_input_iterator<int> begin(data);  
stl_input_iterator<int> end;  
assert(std::distance(begin, end) == std::distance(begin, end));  
```  
  
`std::distance` copies `begin`, but alters its internal state `it_` and so both iterators are changed, and doing `std::distance` again doesn't return the same result.   
  
Fixing this is non-trivial (at least to me) because I can't find an easy way to get an independent copy of `it_` without having the original `object` used to construct the iterator.

---

## Comment 1

> Username: mlogic  
> Created at: 2016-07-16 01:27:21 UTC  
> Url: https://github.com/boostorg/python/issues/69#issuecomment-233100996  

The problems described in this report can be easily triggered by any functions that use the iterators in a non-trivial way. For example I have a math function, which calculates the mean, variance, and confidence interval of an input list, triggers this bug and always crashes because it needs to iterate over the data multiple times by duplicating the iterator. My function is simple and well-understood until we pass in an stl_iterator instance, which cannot be reliably duplicated.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2016-07-16 01:53:45 UTC  
> Url: https://github.com/boostorg/python/issues/69#issuecomment-233102218  

I'll try to look into this when I get a chance. Providing an actual patch (pull request or somesuch) to at least demonstrate the issue, if not to fix it, would be very helpful in fixing this.  
Thanks !

---

## Comment 3

> Username: mlogic  
> Created at: 2016-07-16 02:00:28 UTC  
> Url: https://github.com/boostorg/python/issues/69#issuecomment-233102510  

Like @tussock-cam, my attempted to fix this quickly failed. IMHO the code in the original report is good test cases for this bug. I verified that this bug exists on 1.61. Feel free to let me know if you need more cases or have a patch to test.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2016-07-16 02:03:55 UTC  
> Url: https://github.com/boostorg/python/issues/69#issuecomment-233102656  

Please provide a self-contained test case, i.e. a source file that, when compiled into a binary, demonstrates the failure, together with build instructions (i.e. a specific report including OS and compiler).

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-04-15 18:14:57 UTC  
> Url: https://github.com/boostorg/python/issues/69#issuecomment-381426372  

Given the original issue was reported on `boost::python::numeric`, which has been superseded by `boost::python::numpy`, I'm closing this issue as obsolete.
