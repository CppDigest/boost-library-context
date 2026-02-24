# #845 - Document when value_from is needed [Closed]

> Username: gummif  
> Created at: 2023-01-25 12:16:23 UTC  
> Updated at: 2024-10-09 14:11:33 UTC  
> Closed at: 2024-10-09 14:11:32 UTC  
> Url: https://github.com/boostorg/json/issues/845  

Since 1.81 this no longer compiles:  
```c++  
struct customer  
{  
    int id;  
    std::string name;  
    std::vector<int> vec  
};  
void tag_invoke( value_from_tag, value& jv, customer const& c )  
{  
    jv = {  
        { "id" , c.id },  
        { "name", c.name },  
        { "vec", c.vec }  
    };  
}  
```  
I see from https://github.com/boostorg/json/issues/823 that this is intended. But it is not clear when calling `value_from` is needed and maybe the idiomatic way of doing it should be documented e.g. in the example here https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/quick_look.html. I assume the optimal way to do this now is the following  
```c++  
void tag_invoke( value_from_tag, value& jv, customer const& c )  
{  
    jv = {  
        { "id" , c.id },  
        { "name", c.name },  
        { "vec", value_from(c.vec, jv.storage()) } // note pass storage to use the correct allocator  
    };  
}  
```  
Is this correct?

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-01-25 12:33:18 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-1403539285  

First of all, yes, this code is correct.  
  
Second, I honestly can't see the source of confusion: you need `value_from` when your type is not implicitly convertible to `value`. This is just like with any other type in C++.  Is the problem with seemingly magical behaviour of `std::initializer_list<value_ref>` constructor/assignment?

---

## Comment 2

> Username: Triasmus  
> Created at: 2023-04-02 15:35:41 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-1493373974  

For me, I feel that it would make sense for boost::json to implicitly know to convert std::vector to a json array in this case. I just ran into this issue myself, where the implicit conversion was working fine in 1.80, and now I have build errors in 1.81 (I'm going back to 1.80 with the hope that I won't find the desire to use std::variant or std::optional).  
  
So that's my source of confusion.  
The quick look example page shows that `value jv = {{"list", {1, 0, 2}}};` works. Replacing `{1, 0, 2}` with a pre-initialized `vector<int>` just _makes sense_.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-04-02 17:57:49 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-1493403039  

As, I've mentioned previosuly, the behaviour was never intended. If it was, `json::value jv = std::vector{1,2,3}` would also have been working, but it wasn't. The behaviour was a bug.  
  
`{{"list", {1, 0, 2}}};` does not mean `any_map{ {"list", any_sequence{1, 0, 2}} };`, it means `json::object{ {"list, json::array{1, 0, 2} }`.  
  
I am not aware of any container type that does implicit conversions from other container types. In particular, standard container types definitely do not do that. There is a reason for that: implicit conversions cannot be locally disabled, they are pervasive, they create potential for lots of unintended conversions. They are a particularly terrible idea when  memory is likely to be allocated.

---

## Comment 4

> Username: gummif  
> Created at: 2023-04-09 21:42:27 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-1501219686  

I agree that this is a better design than implicit conversion. My heuristic is if an allocation is probably required then I need value_from and usually pass the storage ptr.

---

## Comment 5

> Username: rjahanbakhshi  
> Created at: 2023-09-07 18:24:48 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-1710595838  

This could be related. In 1.80 the following used to work but in 1.81 it doesn't compile:  
```  
#include <boost/describe/enum.hpp>  
  
enum class states  
{  
    failed,  
    completed,  
};  
BOOST_DESCRIBE_ENUM(states, failed, completed);  
  
#include <boost/json.hpp>  
  
int main()  
{  
    boost::json::value value = {  
        {"id", 42},  
        {"state", states::failed},  
    };  
}  
```  
  
In 1.81 I need to use value_from otherwise it doesn't compile:  
```  
#include <boost/describe/enum.hpp>  
  
enum class states  
{  
    failed,  
    completed,  
};  
BOOST_DESCRIBE_ENUM(states, failed, completed);  
  
#include <boost/json.hpp>  
  
int main()  
{  
    boost::json::value value = {  
        {"id", 42},  
        {"state", boost::json::value_from(states::failed)},  
    };  
}  
```  
  
Of course, in 1.80 I had my own tag_invoke overload for described enum but it is added in 1.81.   
  
It's a pity because the pretty initializer format is not working with 1.81+ for custom types or am I missing something?

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-09-07 19:47:55 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-1710686252  

Yes, this behaviour was never intended and wasn't documented and so we removed the code that accidentally allowed it to work.

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-10-09 14:11:33 UTC  
> Url: https://github.com/boostorg/json/issues/845#issuecomment-2402463481  

To reiterate, `value_from` is needed anywhere where you want to convert an unrelated type to `json::value`.  This is clear in the documentation, and the confusion is the result of accidental behaviour in the older versions of the library.
