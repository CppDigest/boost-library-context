# #758 - boost::compute::valarray should support std::begin and std::end [Open]

> Username: jeffhammond  
> Created at: 2018-01-21 01:33:29 UTC  
> Updated at: 2018-02-24 11:28:22 UTC  
> Url: https://github.com/boostorg/compute/issues/758  

I want to be able to copy a `compute::valarray` to a `std::valarray` like this:  
```c++  
std::valarray<float> h(0.0f,1000);  
compute::valarray<float> d(1.0f,1000);  
compute::copy(std::begin(d), std::end(d), std::begin(h));  
```  
This fails because `begin()` and `end()` are private (http://www.boost.org/doc/libs/1_62_0/libs/compute/doc/html/boost/compute/valarray.html).  
  
In contrast, `begin()` and `end()` are supported for `std::valarray` since C++11 (http://en.cppreference.com/w/cpp/numeric/valarray).  
  
I solved the issue locally with the following trivial change.  
```c++  
//private:  
    buffer_iterator<T> begin() const  
    {  
        return buffer_iterator<T>(m_buffer, 0);  
    }  
  
    buffer_iterator<T> end() const  
    {  
        return buffer_iterator<T>(m_buffer, size());  
    }  
```  
  
Is this an acceptable change?  It would be nice if `boost::compute::valarray` behaved like C++11 `std::valarray`.  
  
I can contribute the trivial change if that is sufficient.  If a more complicated one is required, I'll do my best.

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-01-21 11:15:18 UTC  
> Url: https://github.com/boostorg/compute/issues/758#issuecomment-359240728  

Yeah, I think you can make them public if `BOOST_COMPUTE_USE_CPP11` is defined. You can also look at https://github.com/boostorg/compute/issues/707 if you are interested in `boost::compute::valarray`.
