# #74 - Extra space sometimes inserted after lparen in expansion of function-like macros [Closed]

> Username: jefftrull  
> Created at: 2020-02-27 13:43:45 UTC  
> Updated at: 2020-02-28 13:10:25 UTC  
> Closed at: 2020-02-28 13:10:25 UTC  
> Url: https://github.com/boostorg/wave/issues/74  

If you define a function-like macro whose definition contains parenthesis, Wave will sometimes insert an extra space immediately after the left parenthesis when the macro is expanded. For example:  
```c++  
#define FOO(BAR, BAZ) (BAZ)  
FOO("hello", 3)  
```  
becomes  
```  
( 3)  
```  
Interestingly this seems to only be true for expansion of the _last_ parameter when there is more than one, i.e.  
```c++  
#define FOO(BAR, BAZ) (BAR)  
FOO("hello", 3)  
```  
becomes  
```  
("hello")  
```  
and also  
```c++  
#define FOO(BAR) (BAR)  
FOO(3)  
```  
is correctly  
```  
(3)  
```

---

## Comment 1

> Username: hkaiser  
> Created at: 2020-02-27 14:20:42 UTC  
> Url: https://github.com/boostorg/wave/issues/74#issuecomment-591996264  

This is an interesting one!

---

## Comment 2

> Username: hkaiser  
> Created at: 2020-02-27 20:40:18 UTC  
> Updated at: 2020-02-27 20:43:20 UTC  
> Url: https://github.com/boostorg/wave/issues/74#issuecomment-592166265  

@jefftrull as it turns out this behavior is by design. Wave is much more stringent wrt retaining whitespace that was passed as part of an argument for a macro invocation. Clang and gcc tend to remove whitespace that is not needed for syntactic disambiguation. Assuming:  
```c++  
#define FOO1(BAR, BAZ) (BAR)  
#define FOO2(BAR, BAZ) (BAZ)  
```  
we get  
```c++  
FOO2("hello", 3)       // expands to ( 3)  
FOO2("hello",    3)    // expands to (    3)  
FOO2("hello",3)        // expands to (3)  
```  
(please note the additional whitespace in front of the argument `3`).  
  
That said however, while investigating this issue I noticed that wave removes whitespace following the last argument:  
```c++  
FOO1( "hello" , 3 )    // expands to ( "hello" ) as it should  
FOO2( "hello" , 3 )    // expands to ( 3) but should expand to ( 3 )  
```  
I think this is something that needs a fix.

---

## Comment 3

> Username: jefftrull  
> Created at: 2020-02-28 02:54:32 UTC  
> Url: https://github.com/boostorg/wave/issues/74#issuecomment-592285973  

Thanks for the explanation!  
  
Interestingly, I cannot duplicate your finding that the final space is removed. The output for me is:  
```  
    ( "hello" );   
    ( 3 );   
```

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-02-28 02:55:55 UTC  
> Url: https://github.com/boostorg/wave/issues/74#issuecomment-592286284  

For future readers, the behavior of gcc is described at the bottom of page 15 on [this document](https://gcc.gnu.org/onlinedocs/cpp.pdf):  
  
> Leading and trailing whitespace in each argument is dropped, and all whitespace between  
the tokens of an argument is reduced to a single space.

---

## Comment 5

> Username: hkaiser  
> Created at: 2020-02-28 13:10:25 UTC  
> Url: https://github.com/boostorg/wave/issues/74#issuecomment-592505743  

> Interestingly, I cannot duplicate your finding that the final space is removed. The output for me is:  
>     ( "hello" );   
>     ( 3 );  
  
Yes, I agree. I'm not sure what I was looking at anymore :/ I think we can close this for now.
