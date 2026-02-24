# #190 - boost::python handle& operator=() is not  implemented correctly [Open]

> Username: mgb4  
> Created at: 2018-03-13 16:13:59 UTC  
> Updated at: 2018-03-15 11:43:11 UTC  
> Url: https://github.com/boostorg/python/issues/190  

The assignment operator for a handle is implemented by first decreasing the reference count of the contained object and then incrementing the one of the right hand side.  
  
```python  
python::xdecref(m_p);  
m_p = python::xincref(r.m_p);  
```  
  
This is not the correct order but should be the other way round, i.e. first incrementing than decrementing. This is also the effect described in the documentation.  
  
```python  
python::xincref(r.m_p)  
python::xdecref(m_p);  
m_p = r.m_p  
```  
  
By doing it the current way, self assignment increases the reference count of a potentially already freed object.
