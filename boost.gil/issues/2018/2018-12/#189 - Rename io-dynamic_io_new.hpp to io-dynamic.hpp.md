# #189 - Rename io/dynamic_io_new.hpp to io/dynamic.hpp [Closed]

> Username: mloskot  
> Created at: 2018-12-12 17:14:37 UTC  
> Updated at: 2022-04-27 11:27:13 UTC  
> Closed at: 2022-04-27 11:27:13 UTC  
> Url: https://github.com/boostorg/gil/issues/189  

(/cc @stefanseefeld @chhenning)  
  
@sdebionne suggested in https://github.com/boostorg/gil/pull/185#issuecomment-446516062  
  
> should io/dynamic_io_new.hpp be io/dynamic_io.hpp really?  
  
I think it is a valid suggestion since the current name is unnecessarily complex, may be confusing:  
  
- `new` is obscure: as in new implementation or as in `new`-allocated dynamic memory, etc.  
- `io` is redundant, since all core IO headers live in dedicated directory  
  
I'd expect renaming it is safe from compatibility point, majority of the file is `boost:gil::detail` namespace and just this one lives outside  
  
https://github.com/boostorg/gil/blob/2250b7159c398899ec0fa27246f0d7aacdf2c1fe/include/boost/gil/io/dynamic_io_new.hpp#L91-L96  
  
but possibly it should live in the `detail` as well.  
  
Comments?
