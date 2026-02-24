# #24 - Consider implementing the <=>  for cases unsupported by std::variant [Open]

> Username: libbooze  
> Created at: 2021-03-07 09:17:30 UTC  
> Updated at: 2021-03-07 09:17:30 UTC  
> Url: https://github.com/boostorg/variant2/issues/24  

First of all IDK if there are any spaceship plans for `variant2 `so this is highly speculative.   
  
Secondly I am not some PL expert, and I do not know all C++ corner cases that might make this impossible, this is purely based on what I consider _nicer_ to use.  
  
In the following example to get `std::variant` ` <=>` to work we must provide` <=> `for `std::is_empty_v<T> == true `kind of struct although it has no state.   
  
  
```  
#include<type_traits>  
#include<variant>  
  
struct Empty{  
 // variant <=> does not compile without this line         
 auto operator<=>(const Empty& other) const = default;  
};  
  
static_assert(std::is_empty_v<Empty>);  
  
int main() {  
    std::variant<Empty,int,float> v1,v2;  
    v1<=>v2;  
}  
  
```  
If `<=> `support is added to the `variant2 `it would be nice if he would be able to skip over empty structs when checking that all elements have implemented `<=>` (he would treat all instances of empty structs as being `std::strong_order::equal`).    
  
Convenience motivation for this is that unlike some other languages C++ enums can not carry state( IIRC Rust, Swift... can) so people sometimes use variant of structs as a C++ enum. Now some of those structs are pure "tag" or enum struct without any state, it is a bit of a shame to be required add a `<=> `to them just so they can be put in `variant2`.
