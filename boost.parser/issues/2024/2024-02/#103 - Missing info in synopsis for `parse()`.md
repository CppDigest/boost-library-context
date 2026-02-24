# #103 - Missing info in synopsis for `parse()` [Closed]

> Username: akrzemi1  
> Created at: 2024-02-21 21:51:24 UTC  
> Updated at: 2024-02-28 20:47:07 UTC  
> Closed at: 2024-02-24 04:04:03 UTC  
> Url: https://github.com/boostorg/parser/issues/103  

The [synopsis for parse()](https://tzlaine.github.io/parser/doc/html/boost/parser/parse_idm27047.html) is missing relevant information:  
  
> *Mandates:* `text_input<R>` is `true`, `error_handler<ErrorHandler>` is `true`, `attribute_compatible<Attr, R, Parser>` is `true`.  
>  
> *Throws:* whatever attribute copying or assignemt, or semantic actions throw. (something else?), `std::bad_alloc`?  
>   
> *Returns:* `true` on *successful parse*, when the entire input is consumed; `false` otherwise.  
>   
> *Effects:* on successful parse `attr`  is assigned the value of the attribute produced by `parser`. On unsuccessful parse `attr` is assigned a default-constructed value.  
  
And you may need a concept `attribute_compatible` or at least a "macro" for the purpose of documentation. You may also need to define what "successful parse" means.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-24 04:03:59 UTC  
> Url: https://github.com/boostorg/parser/issues/103#issuecomment-1962243256  

#109 covers the concept part.  The rest is in the description already, just not formatted like this.  The exception is the description of hhow the out-param is compatible with the default attribute.  This is too much to repeat for each `*parse()` overload.  The tutorial covers it, though.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-28 20:47:05 UTC  
> Url: https://github.com/boostorg/parser/issues/103#issuecomment-1969891338  

So, it looks like you are saying that most of the function description is there, just not in a user-friendly format.   
I am requesting to provide a user-friendly format.  
  
Some users will want to jump directly to the reference section and would expect to find all the relevant information here. Or at least the reference to other places that describe more generic properties. When the user wants to learn what `parse()` does, they will not know that `prefix_prse()` is almost the same. So from user's perspective such repetition one every `*parse()` function is rather helpful.
