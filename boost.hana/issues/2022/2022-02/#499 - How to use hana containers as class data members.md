# #499 - How to use hana containers as class data members? [Closed]

> Username: impugachev  
> Created at: 2022-02-10 09:11:33 UTC  
> Updated at: 2022-10-21 03:41:10 UTC  
> Closed at: 2022-10-21 03:41:09 UTC  
> Url: https://github.com/boostorg/hana/issues/499  

For example, I want to write something like this in a class method.  
```c++  
if constexpr (boost::hana::contains(this->map_, "name"_s)){  
    std::cout << this->map_["name"_s] << std::endl;  
}  
```  
Unfortunately, c++ prohibits the use of `this` in `if constexpr`. How am I supposed to use hana here? Sorry if this has already been asked, I couldn't find anything similar.  
  
[Full example](https://godbolt.org/z/jfoEKxM8T)   
[Stupid workaround](https://godbolt.org/z/Gx3scTqrb)

---

## Comment 1

> Username: ldionne  
> Created at: 2022-10-21 03:41:09 UTC  
> Url: https://github.com/boostorg/hana/issues/499#issuecomment-1286417773  

Here's another way to do it: https://godbolt.org/z/W41K57cb6  
  
The fundamental limitation here is that the language sees you are reading `this->...` inside a constant evaluated context and it bails out.
