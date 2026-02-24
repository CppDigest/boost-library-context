# #82 - How to reflect an opitonal element that parses into a vector? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-27 12:04:40 UTC  
> Updated at: 2024-01-27 22:08:45 UTC  
> Closed at: 2024-01-27 22:08:45 UTC  
> Url: https://github.com/boostorg/parser/issues/82  

Is there a way to reflect an optional grammar element (which is itself a list), but have it reflected as an empty `vector` rather than `optional<vector>`?  
  
I have a couple of situations like this in my parser: https://godbolt.org/z/W9xETqxWx  
See the usage of `cond` in  
```c++  
auto redirection_def = +(bp::lit("[->]") >> cond >> (-map) >> label >> set_vars);  
```  
  
And even if I employ a rule parser, I do not see how to do it. Ideally, I would like to say:  
  
```c++  
struct Redirection  
{  
  std::vector<Variable> cond;  
  // other members  
};  
  
const bp::rule<struct cond_tag, std::vector<Variable>> cond = "cond";  
const bp::rule<struct redirection_tag, Redirection> redirection = "redirection";  
  
auto cond_def = "[" >> ((identifier >> "=" > bp::int_) % ",") >> "]";  
auto redirection_def =  /*...*/ >> -cond >> /*...*/;  
```  
  
So that the minus clearly reflects the optionality of the element, but I do not have to pay the penalty of using `optional`.  
  
Is it doable?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-27 22:08:37 UTC  
> Url: https://github.com/boostorg/parser/issues/82#issuecomment-1913350001  

We talked about this before.  You do it like this:  
  
```c++  
auto redirection_def =  /*...*/ >> (cond - bp::attr(std::vector<Variable>{})) >> /*...*/;  
```
