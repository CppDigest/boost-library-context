# #118 - Make `parse_context` more visible in the docs? [Closed]

> Username: akrzemi1  
> Created at: 2024-02-25 22:39:27 UTC  
> Updated at: 2024-03-02 23:37:17 UTC  
> Closed at: 2024-03-02 23:37:17 UTC  
> Url: https://github.com/boostorg/parser/issues/118  

Given that the reference documentation is generated from the header files, I would recommend moving `parse_context` to a separate header. This would make all the context-manipulating funcitions easier to discover.   
  
Also, you probably should make `parse_context` part of the public interface, given that users are encouraged to use it. Theni would be easier to describe all the "underscore" fuctions (like `_val()`) as part of its interface.  
  
Section [The Parse Context](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parse_context.html) looks like a reference. Maybe it should go towards the end of the documentation. All the use cases would be described earlier, and this would serve as a cheat sheet with cross-references.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-26 02:59:10 UTC  
> Url: https://github.com/boostorg/parser/issues/118#issuecomment-1963233723  

the parse context is indeed meant to be used directly by users, but it's an opaque type.  In fact, it's not one opaque type, but a whole family of them, specialized from a template.  So, I can't even really just name the type and move on.  
  
I don't yet get the advantage of publicizing it.  What do you think it would help?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-26 09:18:57 UTC  
> Url: https://github.com/boostorg/parser/issues/118#issuecomment-1963650325  

You could document class template `parse_context`. Can the `ctx` be anything else than the specializaiton of `parse_context`?  
  
You could document only the parts you wish to expose: maybe only the accessors such as  
  
```c++  
    template<typename Context>  
    decltype(auto) _params(Context const & context);  
```   
  
My motivation is the same as for any other part of the library exposed to the customers: I wan to know how it works, how I can use it, how it will compose with other parts, what to fix when the program does not compile, when I need to debug to be familiar with the names and concepts.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-02-27 02:26:42 UTC  
> Updated at: 2024-02-27 02:38:08 UTC  
> Url: https://github.com/boostorg/parser/issues/118#issuecomment-1965674455  

I don't understand this request exaclty.  I did document all of the underscore-functions.  If you want to know what parameters you are allowed to pass to `_params()`, why?  You never call that function.  All you need to know is that *I* will call your semantic action with some type that works with the underscore API.  
  
I already explained in the docs that there's a context object that is the moral equivalent of a bag of key/value pairs, and that it works with that API.  What can the user do if they know that the context object is always a specialization of `detail::parse_context`?  
  
If I document it, people might use its details.  It's meant to be an implementation detail that I can freely change as needed.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-02-27 16:15:09 UTC  
> Url: https://github.com/boostorg/parser/issues/118#issuecomment-1966921385  

This issue was motivated by my experience with the Documentation.    
  
I went to section [The Parse Context](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parse_context.html) and found the description of `_params()` and `_locals()` insufficient to understand what they are doing. So my next thought was to consult section [Reference](https://tzlaine.github.io/parser/doc/html/reference.html) but I had no clue in which header to look.  
  
Second, you just tell me to use the underscore names and you do  not provide the signature of these functions as you do for any other functions. This sounds like the magic used for Boost.Phoenix or Boost.Lambda: "just type it and pray that it works, because if it doesn't you will not know why". Boost.Parser would look less mysterious if we could see these "underscore" functions documented in the same way as all others.

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-02-28 04:40:55 UTC  
> Url: https://github.com/boostorg/parser/issues/118#issuecomment-1968217011  

> This issue was motivated by my experience with the Documentation.  
>   
> I went to section [The Parse Context](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/the_parse_context.html) and found the description of `_params()` and `_locals()` insufficient to understand what they are doing. So my next thought was to consult section [Reference](https://tzlaine.github.io/parser/doc/html/reference.html) but I had no clue in which header to look.  
  
Almost everything is in parser.hpp.  However, a reasonable fix for this part mightbe to make sure that all the `_*()` functions are mentioned in The Parse Context, if they are not already, with links to the longer versions where necessary.  
  
> Second, you just tell me to use the underscore names and you do not provide the signature of these functions as you do for any other functions. This sounds like the magic used for Boost.Phoenix or Boost.Lambda: "just type it and pray that it works, because if it doesn't you will not know why". Boost.Parser would look less mysterious if we could see these "underscore" functions documented in the same way as all others.  
  
Ok, I get that, but how are things going to get better if you can see the `parse_context` template?  The "just pray" part comes from the fact that a given call `_foo(ctx)` may or may not be well-formed, and you cannot tell if it will be or not from seeing "`ctx`" in your code.  The well-formedness of that call is entirely down to which template paramaters appear in the exact specialization of `parse_context` that you happen to get passed to your semantic action.  Knowing what the `parse_context` template looks like won't help.
