# #29 - Macro closures [Closed]

> Username: hirrolot  
> Created at: 2020-05-22 10:52:08 UTC  
> Updated at: 2020-09-19 00:25:56 UTC  
> Closed at: 2020-09-19 00:25:56 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29  

I've already [implemented](https://github.com/Hirrolot/libpoica/blob/master/src/meta/mclosure.h) macro closures (anonymous macros). I can adapt them to Boost Preprocessor, are you interested in it?

---

## Comment 1

> Username: eldiener  
> Created at: 2020-05-22 12:57:57 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-632677395  

What are "macro closures" ?

---

## Comment 2

> Username: hirrolot  
> Created at: 2020-05-22 13:01:19 UTC  
> Updated at: 2020-05-22 13:16:44 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-632678771  

Like usual closures, but behaving as macros, not functions. For example:  
  
```c  
int main(void) {  
    // Prints 16.  
    int square = EXEC_MCLOSURE(MCLOSURE(ARG * ARG), 4);  
    printf("%d\n", square);  
}  
```  
  
Which is identical to:  
  
```c  
#define SQUARE(number) ((number)*(number))  
  
int main(void) {  
    // Prints 16.  
    int square = SQUARE(4);  
    printf("%d\n", square);  
}  
```

---

## Comment 3

> Username: eldiener  
> Created at: 2020-05-22 13:12:20 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-632683531  

I am not familiar with the term "closure" in C++. Does it correspond to anything in that language ?  
  
So a macro closure is some expresssion, with names which represent "variables" to be filled in when the macro enclosure is invoked, is that the idea ?

---

## Comment 4

> Username: hirrolot  
> Created at: 2020-05-22 13:15:58 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-632685155  

Yes, this is the idea. It corresponds to https://www.cprogramming.com/c++11/c++11-lambda-closures.html in C++.

---

## Comment 5

> Username: eldiener  
> Created at: 2020-05-22 13:33:12 UTC  
> Updated at: 2020-05-22 13:34:08 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-632692713  

I am familiar with C++ lambdas but did not realize they were also called closures. I would be interested in adding your macro closures into Boost PP and/or Boost VMD, since the latter is purely variadic macros, while this library does still support compilers which do not implement variadic macros although there are few of these left.  
  
I do need some good documentation about your macro closures, such as how many variables can be in the expression, what operators the expression supports, any limitations on the name of the variables in the expression, and such. Also some tests for macro closures need to be written up and added to the test framework. Finally some sort of documentation explaining why macro closures would be preferable to specific macros. But a PR for macro closures would certainly be welcome by me. I agree that it is an interesting idea and I give you full, credit for conceiving of it and implementing it. But a practical case for macro closures still needs to be made <g>.

---

## Comment 6

> Username: hirrolot  
> Created at: 2020-05-22 13:37:32 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-632694580  

Thank you, I'll send a PR when I finish!

---

## Comment 7

> Username: hirrolot  
> Created at: 2020-08-09 09:29:48 UTC  
> Updated at: 2020-08-09 09:33:31 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-671029547  

Apologize for the late response. I came up with this: https://gist.github.com/Hirrolot/c45af231a3c21af5448df4d5b422add3.  
However, such a design clearly obliterates the expressive power of closures, since almost any closure would consist of `BOOST_PP_CLOSURE_ARG_*`, `BOOST_PP_CLOSURE_COMMA`, `BOOST_PP_CLOSURE_RPAREN` and so on.

---

## Comment 8

> Username: eldiener  
> Created at: 2020-08-09 15:33:44 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/29#issuecomment-671066251  

I need an example how your macro closure is supposed to work. I assume a macro closure works with other macros, but I need to see how it is supposed to fit togeher. Documentation would also be welcome.
