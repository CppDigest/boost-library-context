# #1803 - For C++17, support tuple-like access to fields::value_type [Closed]

> Username: Eelis  
> Created at: 2020-01-16 19:47:47 UTC  
> Updated at: 2020-05-05 00:21:33 UTC  
> Closed at: 2020-05-05 00:21:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1803  

This would enable convenient looping like  
  
    for (auto const & [name, value] : fields) { ... }  
  
See [spec](http://eel.is/c++draft/dcl.struct.bind) and [example](http://eel.is/c++draft/pair.astuple).

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-30 14:36:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1803#issuecomment-580282097  

@Eelis   
@vinniefalco   
  
The field type in beast models a tuple of three types:  
* The field enum  
* The field name  
* The field value  
  
The proposal here understandably mentions name and value. In this case, were you thinking that the name should be the text representation of the name?

---

## Comment 2

> Username: Eelis  
> Created at: 2020-01-30 14:42:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1803#issuecomment-580285168  

@madmongo1 Ah good point, I hadn't considered that.  
  
Personally I think I would prefer getting all three components. So it would become:  
  
    for (auto const & [name, name_str, value] : fields) { ... }  
  
(Or in another order, doesn't matter to me.)

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-01-30 20:22:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1803#issuecomment-580442620  

The implementation of basic_fields is such that:  
* dereferencing its iterator returns a reference to a non-copyable type  
* the accessors of this type do not naturally represent bindings to data references.  
  
The upshot of this is that while we can implement this feature, it will result in the situation where the only legal binding will be:  
  
`const auto& [ name, name_str, value ] = *iter`  
  
In which name_str and value will actually be immutable values which represent data references (string_view)  
  
It is my view that this would be very surprising to users.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-01-30 20:23:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1803#issuecomment-580443058  

I agree, it would be surprising.  
  
Even in the best of all possible worlds, the structured binding variables will only be a synonym for `it->name()`, `it->name_string()`, and `it->value()` . Nothing meaningful can be done with these variables in terms of modification, they can only be inspected, and this is a consequence of the immutable-view design. Therefore I see little value in supporting the structured binding, as it is limited simply to supporting an alternate spelling. But there is also danger, because we are giving users something that does not behave the way they expect. Therefore, better not to offer it at all. It is also less work to maintain.

---

## Comment 5

> Username: Eelis  
> Created at: 2020-01-30 20:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1803#issuecomment-580456875  

Understood, thanks for looking into it!
