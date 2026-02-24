# #297 - dynamic_cast<np:ndarray *>(pyobj) [Closed]

> Username: sakex  
> Created at: 2020-03-25 12:57:30 UTC  
> Updated at: 2020-03-25 13:43:56 UTC  
> Closed at: 2020-03-25 13:33:53 UTC  
> Url: https://github.com/boostorg/python/issues/297  

I have a function with an optional second argument, this function can either take one or two matrices. Python gives `None` to optional arguments that haven't been specified. I didn't find a way to pass `None` from python as a `np::ndarray`. The only way I found was to typecast the the `python::object` to a `void * ptr` then back to a `np::ndarray *`. Is there a clean way to do that?  
  
    def("fn", &fn, (python::arg("X"), python::arg("X2") = python::object()));  
  
```  
np::ndarray fn(np::ndarray &X, python::object &X2) {  
    if (X2.ptr() == python::object().ptr()) { // None case  
        return do_something(X, X);  
    }  
    // We need to check that we indeed provided a np::array  
    assert(X2.ptr()->ob_type == X.ptr()->ob_type);  
    void *ptr = &X2;  
    np::ndarray *deref = {static_cast<np::ndarray *>(ptr)};  
    return do_something(X, *deref);  
}  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-03-25 13:19:15 UTC  
> Url: https://github.com/boostorg/python/issues/297#issuecomment-603834176  

I believe the code could be simplified a bit.  
  
> |np::ndarray fn(np::ndarray &X, python::object &X2) {|  
  
Pass boost::python::object arguments by value; they already have   
shared-pointer semantics:  
  
     np::ndarray fn(np::ndarray X, python::object X2) {  
  
> |if (X2.ptr() == python::object().ptr()) { // None case|  
  
This should simply become  
  
     if (X2 == python::object) { // None case  
  
> |return do_something(X, X); } // We need to check that we indeed   
> provided a np::array assert(X2.ptr()->ob_type == X.ptr()->ob_type);   
> void *ptr = &X2; np::ndarray *deref = {static_cast<np::ndarray   
> *>(ptr)}; return do_something(X, *deref);|  
  
I would recommend using `boost::python::extract<>` for this:  
  
     return do_something(X, boost::python::extract<np::ndarray>(X2));  
  
If the conversion fails, `extract` throws an exception.  
  
See   
https://www.boost.org/doc/libs/1_72_0/libs/python/doc/html/reference/to_from_python_type_conversion.html#to_from_python_type_conversion.boost_python_extract_hpp.example  
  
> |}|

---

## Comment 2

> Username: sakex  
> Created at: 2020-03-25 13:33:50 UTC  
> Url: https://github.com/boostorg/python/issues/297#issuecomment-603841982  

Perfect, thank you very much, that's what I was looking for!

---

## Comment 3

> Username: sakex  
> Created at: 2020-03-25 13:42:53 UTC  
> Url: https://github.com/boostorg/python/issues/297#issuecomment-603846626  

One comment, this line failed to compile  
  
    if (X2 == python::object) { // None case  
  
I just added a `()` after object

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2020-03-25 13:43:56 UTC  
> Url: https://github.com/boostorg/python/issues/297#issuecomment-603847132  

yup, noticed my typo right after sending. :-)
