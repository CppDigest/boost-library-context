# #1068 - tag_invoke with non const ref to json value [Open]

> Username: mous16  
> Created at: 2025-01-21 09:08:40 UTC  
> Updated at: 2025-01-21 09:42:53 UTC  
> Url: https://github.com/boostorg/json/issues/1068  

In my code I would need to extract non-const pointers to underlying json values through functions such as `if_int64` during JSON to C++ object conversion.  
I see that all the `if_` function implement a non-const overload, so I guess it's feasible.  
My problem arises when i try to implement my custom `tag_invoke`.  
  
The `tag_invoke` function for `value_to` usage has this form:  
```c++  
T tag_invoke( const value_to_tag< T >&, const value& );  
```  
I would need an overload where the value argument is passed as non-const reference, to be able to obtain non-const pointer to inner objects.  
There is a specific reason why this overload is not present?  
  
Delving into possible UB, I played with `const_cast` and I was unable to trigger any problem in code like the following:  
```c++  
class MyType {  
  
    private:  
    int64_t * my_inner_value;  
  
    public:  
  
    MyType(int64_t * my_inner_value) : my_inner_value{my_inner_value} {}  
  
    const int64_t & get_my_inner_value() const { return *my_inner_value; }  
    void set_my_inner_value(const int64_t & value) { *(this->my_inner_value) = value; }  
};  
  
MyType tag_invoke(json::value_to_tag<MyType>, const json::value & v)  
{  
    auto& o = const_cast<json::object&>(v.as_object());  
  
    return { o.at(myKey).if_int64() };  
}  
```  
  
But that smells like a possible bug in my program.  
To my surprise, checking the code of non-const accessor for internal pointers, I saw that the same const cast approach is used.  
Can I assume that my implementation will not trigger UB and will be reliable?

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-01-21 09:42:52 UTC  
> Url: https://github.com/boostorg/json/issues/1068#issuecomment-2604183932  

You can `const_cast` from a const pointer or reference, if you know that the referenced/pointed to object is not in fact const. Otherwise it would be UB. So,   
  
```  
value jv = ...  
value_to<MyType>(jv);  
```  
  
is not UB, but  
  
```  
value const jv = ...  
value_to<MyType>(jv);  
```  
is.  
  
Anyways, your `tag_invoke` overload stores a pointer to a potentially temporary object. This IMO has a higher chance of causing undefined behaviour. Can you explain why you need to do this?
