# #74 - The interface for defining rules is uncomfortable [Closed]

> Username: akrzemi1  
> Created at: 2024-01-21 20:35:26 UTC  
> Updated at: 2024-01-22 20:50:07 UTC  
> Closed at: 2024-01-22 20:50:06 UTC  
> Url: https://github.com/boostorg/parser/issues/74  

I mean this issue to be a part of Boost Review.  
  
The interface for defining rules in Boost.Parser has certain shortcommings relative to the similar feature in Boost.Spirit.  
  
First, it uses macros. And one may ask if in C++17 we need to use macros for this. Especially that Boost.Spirit didn't have to.  
  
Second, it uses macros in a potentially disturbing way. It departsfrom the referential transparency that good macros usually follow: it depends on the existence of names in the user space, but they are not mentioned as macro parameters. I refer to this:  
  
```c++  
BOOST_PARSER_DEFINE_RULES(doubles);  
```   
  
And this fails to compile because it expected that I would also define `doubles_ref`. When a new person comes to maintain code using this library, this will add to the steep learning curve. Also, if I have a convetion to append `_ref` to some variables for my own purposes, I may find that my conventions can no longer work when I start to use Boost.Parser.  
  
Imagine that another library, say SuperBindings, also introduced such convention: that you have to use macros and that variables need to have shadow variables with a prefix of `_ref`. Then I wouldn't be able to use SuperBindings and Boost.Parser together. This reminds me of the constraints of C,where you do not have namespaces, and every library has to prefix its function with some `gl_`, and hope that it picked a unique prefix.  
  
Also, when it is a macro, my intuition fails me as to where I can put it. If It was a variable, or a function, I would know where in my program to put it, because this follows normal C++ rules. When it is a macro, it can be any chunk of the syntax and I have no intuition where to put it? Can I put it at block scope? I learned that I cannot: I need to put it in a namespace-scope. But *any* namspace, or does it have to be some specific namespace?  
  
Also, if I ever need to debug the program, the debugger will take me to declaraitons tht I never put in my code, and I do not recognize, because they were hidden under a macro.  
  
Third, it forces me to declare rules in (some) namespace scope. So I cannot declare rules at block scope. But this was possible with Boost.Spirit. Boost.Spirit also offered me a way to specify the diagnostic text only when I want it, and not force me to do it always, even if I don't need it.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-21 21:03:47 UTC  
> Url: https://github.com/boostorg/parser/issues/74#issuecomment-1902762835  

This is weird.  Where do you think I got this idiom from?  It comes from from Boost.Spirit.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-21 21:54:57 UTC  
> Url: https://github.com/boostorg/parser/issues/74#issuecomment-1902778833  

My knowledge of Boost.Spirit rules comes from this part of the docs: https://www.boost.org/doc/libs/1_84_0/libs/spirit/doc/html/spirit/qi/reference/nonterminal/rule.html  
  
Also, a live example:  
https://godbolt.org/z/E4dzj5Gah

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-21 22:04:30 UTC  
> Url: https://github.com/boostorg/parser/issues/74#issuecomment-1902781103  

Spirit 2 is a very old library.  The later work on Spirit looks like this:  
  
https://live.boost.org/doc/libs/1_68_0/libs/spirit/doc/x3/html/spirit_x3/tutorials/roman.html#__tutorial_spirit_define__

---

## Comment 4

> Username: akrzemi1  
> Created at: 2024-01-22 20:50:06 UTC  
> Url: https://github.com/boostorg/parser/issues/74#issuecomment-1904783286  

Thanks for the link.  
In that case this Issue doesn't make much sense.  
I must say that to the docs for the X3 version are not easily reachable from the Boost docs page.
