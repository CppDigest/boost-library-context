# #99 - Add indexing operators [Open]

> Username: nbecker  
> Created at: 2017-01-25 13:51:55 UTC  
> Updated at: 2017-02-10 13:51:07 UTC  
> Url: https://github.com/boostorg/python/issues/99  

Thanks for moving forward with this work.  
  
My usage is often porting C code to use with python.  In that case, the C code expects containers that are indexable.  I can often convert to using ndarray (that is, https://github.com/ndarray/ndarray), using pretty simple mechanical translation.  These codes expect to index into arrays.  
  
Without indexing operators, I don't think the current version is useful here.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-01-25 20:51:11 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275229046  

Could you provide a bit more context ? What specifically are you referring to with "this work" :-) ?  
And what classes / objects are lacking indexing operators ? (Can you provide a line or two of Python code that should work but doesn't ?)

---

## Comment 2

> Username: nbecker  
> Created at: 2017-01-26 12:21:28 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275375851  

"This work" refers to the boost::python interface to numpy Array.  
  
Referring to [https://boostorg.github.io/python/develop/doc/html/numpy/reference/ndarray.html](url) it seems the c++ interface to ndarray doesn't provide any indexing operators.  Suppose I write a python extension module in c++ which expects a numpy array:  
  
```c++  
void F(ndarray const& X) {  
  for (int i = 0; i < nrows; ++i) {  
    for (int j = 0; j < ncols; ++j) {  
       X[i][j] *= 2;  
   }  
}  
```  
  
I would assume I could pass a numpy object from python to c++ as shown above, but what I can then do with it is limited if it doesn't support indexing.  
  
I would like to have actually 2 kinds of indexing provided:  
X[i][j]... C-style  
and  
X(i,j,...) probably easier to implement  
  
I think the C-style (which is also implemented in boost::multi_array) requires first implementing views, which is a nice feature, but I'm guessing harder to implement than the 2nd style.  
  
The use case is taking C++ code (legacy) and wrapping it for python, with minimal (hopefully mechanical) changes.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-01-26 13:54:06 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275393053  

Try  
```  
void F(ndarray const& X) {  
  for (int i = 0; i < nrows; ++i) {  
    for (int j = 0; j < ncols; ++j) {  
       X[i,j] *= 2;  
   }  
}  
```  
(Actually, `X[i][j]` also works, as `X[i]` returns a sub-array, which you then can index further.)  
Let me know if that doesn't work, or results in unexpected behavior.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-01-26 14:02:44 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275394898  

(And just to be clear: the reason why you didn't find this documented in the ndarray docs is that it's actually part of the standard object protocol. It might be worthwhile pointing out, though, or adding at least an example to the tutorial if one isn't already there.)

---

## Comment 5

> Username: nbecker  
> Created at: 2017-01-26 14:49:08 UTC  
> Updated at: 2017-01-26 14:50:36 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275406301  

```c++  
#include <boost/python/numpy.hpp>  
#include <boost/python/module.hpp>  
#include <boost/python/def.hpp>  
  
namespace bp = boost::python;  
namespace np = boost::python::numpy;  
  
double sum (np::ndarray const& X) {  
  return X[0,0];  
}  
  
BOOST_PYTHON_MODULE(test1) {  
  // Initialize the Python runtime.  
  Py_Initialize();  
  // Initialize NumPy  
  np::initialize();  
  
  bp::def ("sum", &sum);  
}  
```  
  
```  
test1.cc: In function 'double sum(const boost::python::numpy::ndarray&)':  
test1.cc:9:14: warning: left operand of comma operator has no effect [-Wunused-value]  
   return X[0,0];  
              ^  
test1.cc:9:15: error: cannot convert 'boost::python::api::const_object_item {aka boost::python::api::proxy<boost::python::api::const_item_policies>}' to 'double' in return  
   return X[0,0];  
```  
  
I didn't think it actually works to overload operator "[]" (a,b,c) in c++ with more than one argument.  That's why I suggested (and others have used) either [a][b] or (a,b,c).

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-01-26 15:33:59 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275418845  

Oh, sorry, I was *thinking* about the Python side while *typing* C++ :-)  
You are right, there is no way to pass more than one argument to the '[]' operator, so you should use the '[][]' notation. The reason for the above error, though, is that the return type is a `bp::object`, which you need to extract the double from, e.g. `  return bp::extract<double>(X[0][0]);`

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2017-01-26 15:46:05 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275422540  

(But using this per-element conversion will be slow if you have large arrays, at which point you may prefer raw data access, which is documented here: http://www.boost.org/doc/libs/1_63_0/libs/python/doc/html/numpy/tutorial/fromdata.html

---

## Comment 8

> Username: nbecker  
> Created at: 2017-01-26 16:59:07 UTC  
> Url: https://github.com/boostorg/python/issues/99#issuecomment-275444345  

Now you understand what I was saying - on the c++ side you only have raw pointer access (I was not considering the operator "[]" which uses the object interface and therefore a lot of overhead).  So if you want to use indexing, you'll have to do it yourself.  
  
So what I was suggesting is to add operator overload for "[]" and/or "()" to provide indexing.  
  
Note that the ndarray class [https://github.com/ndarray/ndarray](url) does provide this, but also a lot more.
