# #192 - Add support for non-aggregate initializable struct [Closed]

> Username: bansan85  
> Created at: 2024-11-21 11:01:06 UTC  
> Updated at: 2025-06-20 14:04:47 UTC  
> Closed at: 2025-06-20 14:04:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/192  

[Glaze](https://github.com/stephenberry/glaze) uses a trick to support non-aggregate initializable struct:  
  
```cpp  
template <>  
struct glz::meta<my_struct> {  
   using T = my_struct;  
   static constexpr auto value = object(  
      &T::i,  
      &T::d,  
      &T::hello,  
      &T::arr,  
      &T::map  
   );  
};  
```  
  
Maybe pfr could use something like that ?

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-06-20 14:04:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/192#issuecomment-2991763615  

Nope, that's definitely out of scope of the library :(
