# #493 - Strange behavior with ternary operator [Open]

> Username: sadseeker  
> Created at: 2025-06-20 13:50:11 UTC  
> Updated at: 2025-10-24 21:08:17 UTC  
> Url: https://github.com/boostorg/python/issues/493  

I noticed strange behavior while trying to use ternary operator with `boost::python::dict` and `list`.  
I'm trying to do something like this  
```cpp  
using py = boost::python;  
  
py::dict globals;  
py::dict dict;  
py::list list;  
bool condition = true;  
globals["mol"] = condition ? dict : list;  
py::exec("print(type(mol))", globals);  
```  
  
What I expected: `<class 'list'>` or `<class 'dict'>`  
What I got: `<class 'Boost.Python.function'>`  
  
Is it expected behavior? Then can someone explain me why?  
Or this is a bug?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2025-06-20 14:47:45 UTC  
> Updated at: 2025-06-20 14:50:16 UTC  
> Url: https://github.com/boostorg/python/issues/493#issuecomment-2991911804  

I wouldn't be surprised if this behaviour is the result of expression templates at work.  
Try using a temporary variable to prevent lazy evaluation of the expression on the right-hand side.  
Also, to confirm: what is the result of calling the function you got ? That is, what is the output of  
`py::exec("print(type(mol()))", globals);` ?

---

## Comment 2

> Username: sadseeker  
> Created at: 2025-06-20 14:57:18 UTC  
> Updated at: 2025-06-20 16:37:05 UTC  
> Url: https://github.com/boostorg/python/issues/493#issuecomment-2991936174  

Thanks for answer.  
I can fix this by using simple if-else or casting `dict` and `list` to `py::object` explicitly.  
But I wonder if this is expected behavior or bug  
  
---  
  
> py::exec("print(type(mol()))", globals); ?  
  
Probably `<class 'list'>` because I saw in debugger that `&object::ptr` would be written to `globals["mol"]`.  
Although I can't test right now  
  
---  
  
PS:  
> py::exec("print(type(mol()))", globals); ?  
  
output:  
```  
PythonException:  
Type: ArgumentError  
Value: Python argument types in  
None.None()  
did not match C++ signature:  
None(class boost::python::api::object {lvalue})  
Traceback:  
 <string> : 1  
```

---

## Comment 3

> Username: e-kwsm  
> Created at: 2025-10-24 21:08:17 UTC  
> Url: https://github.com/boostorg/python/issues/493#issuecomment-3444916706  

```cpp  
globals["mol"] = condition ? dict : list;  
```  
  
Here `dict` and `list` *must have the same type*.  
  
Try the folowing code  
  
```cpp  
  globals["foo"] = true ? false : 123;  // false is converted to (int)0  
  globals["bar"] = false;  
  boost::python::exec("print(foo, bar)", globals);  
```  
  
which prints  
```  
0 False  
```  
  
See <https://stackoverflow.com/a/8535301>.
