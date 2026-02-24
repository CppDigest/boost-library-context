# #356 - Accessing converted C++ class from python is 10-20 times slower [Open]

> Username: leryss  
> Created at: 2021-03-09 20:25:52 UTC  
> Updated at: 2021-12-20 12:49:25 UTC  
> Url: https://github.com/boostorg/python/issues/356  

What is boost python actually doing when accessing a C++ class field from python. I could live with the overhead from calling functions but just accessing int/bool fields is 10-20 slower than a native python class.  
  
Tested this by making two identical classes one in native python one in C++ and accessing a single integer field. Access in the C++ class seems to be 10 times slower. Can I do anything to improve this ?

---

## Comment 1

> Username: duburcqa  
> Created at: 2021-12-20 12:49:25 UTC  
> Url: https://github.com/boostorg/python/issues/356#issuecomment-997895631  

I'm wondering too. But I noticed this limitation quite a long time ago (more than 5 years) and therefore when I need efficiency I always wrap the data as  numpy scalar, so that I can return a reference from C++ to Python and create a wrapper around the bindings to return the numpy scalar instead of accessing the field of the underlying C++ object. It is tedious and partially defeat the purpose on boost python I did not find any other solution...
