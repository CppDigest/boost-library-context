# #92 - Get structure padding [Open]

> Username: NN---  
> Created at: 2021-09-04 08:11:59 UTC  
> Updated at: 2022-03-12 11:52:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/92  

Is it possible to calculate structure padding using this library ?

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-09-10 16:29:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-917039797  

Yes. `sizeof(your_structure) - sizeof(all fields)`,  iterate over the fields using this library.  
Or do you mean alignment? Than just use `alignof`

---

## Comment 2

> Username: NN---  
> Created at: 2021-09-10 16:31:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-917040771  

I mean to have a predefined constexpr function in the library.

---

## Comment 3

> Username: apolukhin  
> Created at: 2021-09-12 14:55:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-917652021  

I'm still not shure what do you want to get. Please, provide a sample implementation of such function.

---

## Comment 4

> Username: NN---  
> Created at: 2021-09-12 15:20:07 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-917656105  

```cpp  
template<typename T>  
void f(T&& value)  
{  
static constexpr size_t padding = get_padding<T>();  
if constexpr (padding > 8){  
 Do this  
}  
else{  
 Do that   
}  
}  
```

---

## Comment 5

> Username: apolukhin  
> Created at: 2021-09-29 09:19:07 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-929999860  

I need a more usable example to understand what are the use-cases and what API suits those cases best. For what do you need the padding and how are you planning to use it?

---

## Comment 6

> Username: NN---  
> Created at: 2021-09-29 09:26:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-930005400  

My intent was to automatically detect structure padding and use it for memory optimization if there is a padding.  
For instance `optional<MyStruct>` doesn't need to waste additional 8 bytes for a single boolean.

---

## Comment 7

> Username: technic  
> Created at: 2021-11-09 22:09:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-964591104  

I think you can use something like this  
  
```cpp  
template<typename T>  
consteval bool get_padding() {  
    namespace hana = boost::hana;  
  
    auto field_size = [](auto i) {  
        return sizeof(boost::pfr::get<i>(std::declval<T>()));  
    };  
  
    constexpr size_t count = boost::pfr::tuple_size_v<T>;  
    auto range = hana::make_range(hana::int_c<0>, hana::int_c<count>);  
  
    constexpr size_t result = hana::unpack(range, [=](auto... is) {  
        return (0 + ... + field_size(is));  
    });  
    return sizeof(T) - result;  
}  
```

---

## Comment 8

> Username: NN---  
> Created at: 2022-03-12 11:52:15 UTC  
> Updated at: 2022-03-12 11:52:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/92#issuecomment-1065869356  

@technic I see that I was not clear in the original request.  
This approach does calculates the padding but it doesn't say where the padding is.  
For instance given  
  
```cpp  
struct A{  
  int a;  
  char b;  
  int c;  
  chat d;  
  int e;  
};  
```  
  
The padding could be 6 bytes in the middle and 0 bytes in the end.  
  
My original intent is to use this padding for internal purposes to reduce memory usage.  
  
Thanks.
