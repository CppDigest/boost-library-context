# #101 - Import mangled symbol std::span<float> [Open]

> Username: maximiliank  
> Created at: 2025-06-30 17:37:03 UTC  
> Updated at: 2025-07-27 13:05:56 UTC  
> Url: https://github.com/boostorg/dll/issues/101  

I am trying to import a symbol which has `std::span<float>` as an argument with `boost::dll::experimental::smart_library`. I am using `gcc` 14.2.0 and my exported function is:  
  
```cpp  
void compute_kernel_cpp(std::span<float> data);  
```  
  
When I try to import it via  
  
```cpp  
boost::dll::experimental::smart_library library("mylib.so");  
boost::dll::experimental::import_mangled<void(std::span<float>)>(library, "compute_kernel_cpp");  
```  
  
it is not finding the symbol. The reason is that it searches for `compute_kernel_cpp(std::span<float>)` (which is obtained via `boost::typeindex::ctti_type_index::type_id<std::span<float>>()`), but the demangled symbol in the `.so` file is called `"compute_kernel_cpp(std::span<float, 18446744073709551615ul>)"`. It works if I explicitly specify a non dynamic extent to span, e.g. `std::span<float, 2>`, but it seems the `std::dynamic_extent` is getting lost somewhere.  
  
Is there a way to work around this issue?
