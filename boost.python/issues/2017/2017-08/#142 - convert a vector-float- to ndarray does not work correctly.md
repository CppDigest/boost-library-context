# #142 - convert a vector<float> to ndarray does not work correctly [Closed]

> Username: bpkcl630  
> Created at: 2017-08-01 12:05:02 UTC  
> Updated at: 2017-08-07 13:21:10 UTC  
> Closed at: 2017-08-07 13:21:10 UTC  
> Url: https://github.com/boostorg/python/issues/142  

Hi,  
  
I have a trouble when using boost 1.64.0 to convert a vector<float> to a 2D ndarray.  
  
I try to use code like this   
  
`np::ndarray pyArray = np::from_data(vec.data(), np::dtype::get_builtin<float>(), np::tuple(rows, cols), py::make_tuple(cols, 1), py::object() );`  
  
when I return it to python the result is not same as C++. Then I change this to another way which is  
  
`np::ndarray result = np::zeros(np::tuple(rows, cols), np::dtype::get_builtin<float>());`  
`std::copy(vec.begin(), vec.end(), reinterpret_cast<float*>(result.get_data()));`  
  
and it is works correctly now.  
  
I am not sure it is a bug or I use the from_data() in a wrong way.  
  
Thanks a lot for your help!

---

## Comment 1

> Username: bpkcl630  
> Created at: 2017-08-03 05:09:37 UTC  
> Url: https://github.com/boostorg/python/issues/142#issuecomment-319869250  

forgot to mention I am on 64bit Windows 10.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-08-03 15:48:19 UTC  
> Url: https://github.com/boostorg/python/issues/142#issuecomment-320009667  

I suspect the error is that you specify strides in units of elements, when it really needs to be units of bytes. Please replace `py::make_tuple(cols, 1)` by `py::make_tuple(cols*sizeof(float), sizeof(float))`, and see whether that gives you the right data.

---

## Comment 3

> Username: bpkcl630  
> Created at: 2017-08-07 08:36:15 UTC  
> Url: https://github.com/boostorg/python/issues/142#issuecomment-320603890  

Thanks a lot! That do works.
