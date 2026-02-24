# #227 - Mutually recursive attribute types [Closed]

> Username: psiha  
> Created at: 2025-04-23 18:23:15 UTC  
> Updated at: 2025-05-05 11:15:49 UTC  
> Closed at: 2025-04-26 22:26:11 UTC  
> Url: https://github.com/boostorg/parser/issues/227  

Please provide support for (and/or document how to do) mutually recursive attribute types, eg:  
```  
struct Expression {  
    Atomic atom;  
};  
struct Atomic {  
    std::variant<Literal, Expression> value;  
};  
```  
...like the spirit::x3::forward_ast thingy.  
  
  
ps. thanks for making this (library:)

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:26:11 UTC  
> Url: https://github.com/boostorg/parser/issues/227#issuecomment-2832665004  

Well, you can do anything you want via semantic actions 00 just attach the right functions in the right places, and that is doable.  I don't think this is a common enough use case to build in explicit support for it outside of that.

---

## Comment 2

> Username: psiha  
> Created at: 2025-05-05 11:15:47 UTC  
> Url: https://github.com/boostorg/parser/issues/227#issuecomment-2850670136  

> I don't think this is a common enough use case to build in explicit support for it outside of that.  
  
This is in basic calculator examples  
https://github.com/boostorg/spirit/blob/develop/example/x3/calc/calc6.cpp  
So X3 offers it as well as boost::variant (w/ boost::recursive_wrapper).  
And it does not have to be built-in ('core') support - just a shipped utility, for a truly recurring use case - to maintain your 'library independence'.  
E.g. the x3::forward_ast thingy works with boost::parser (but is awkward to bring in x3 just for that).
