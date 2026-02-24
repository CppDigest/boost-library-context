# #16 - Is there a way to add another directive that behaves as #include ? [Closed]

> Username: malickf  
> Created at: 2017-09-23 17:16:41 UTC  
> Updated at: 2017-11-22 14:19:48 UTC  
> Closed at: 2017-11-22 14:19:48 UTC  
> Url: https://github.com/boostorg/wave/issues/16  

I 'm trying to preprocess a file that has a C like language.  
However the main difference is that it allows both #include or #import statement.   
  
ex :  
#import <maximize.h>  
**and**  
#include <maximize.h>  
  
They both behave in the standard way.   
I have tried to modify the custom_directive_hooks example without success :   
  
```  
found_unknown_directive(ContextT const& ctx, ContainerT const& line,   
        ContainerT& pending)  
    {  
        namespace wave = boost::wave;  
  
        typedef typename ContainerT::const_iterator iterator_type;  
        iterator_type it = line.begin();  
        wave::token_id id = wave::util::impl::skip_whitespace(it, line.end());  
  
        if (id != wave::T_IDENTIFIER)  
            return false;       // nothing we could do  
  
        if ((*it).get_value() == "import" )  
        {  
             //-------------->  how to  map to "include" here ?  
           std::copy(line.begin(), line.end(), std::back_inserter(pending));  
            return true;  
        }  
  
        return false;           // unknown directive  
    }  
```  
Could you help me with this ?  
  
Many thanks,   
  
Malick

---

## Comment 1

> Username: hkaiser  
> Created at: 2017-09-23 17:42:41 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-331657022  

There is an example demonstrating how to add your own directives. See: https://github.com/boostorg/wave/tree/develop/samples/custom_directives.

---

## Comment 2

> Username: hkaiser  
> Created at: 2017-09-23 17:51:02 UTC  
> Updated at: 2017-09-23 17:54:01 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-331658058  

Also, here is the code in wave which handles `#include` directlives: https://github.com/boostorg/wave/blob/develop/include/boost/wave/util/cpp_iterator.hpp#L1549-L1730

---

## Comment 3

> Username: malickf  
> Created at: 2017-09-23 18:13:24 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-331660189  

Thanks but the library is very sophisticated, and the example is very simple with respect to the task I want to implement.. Should I call `on_include` within `found_unknown_directive` ? could you please make me a small example ?

---

## Comment 4

> Username: hkaiser  
> Created at: 2017-09-23 18:15:13 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-331660307  

> could you please make me a small example ?  
  
I can try, but this might take a while before I get to it.

---

## Comment 5

> Username: malickf  
> Created at: 2017-09-23 18:18:03 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-331660477  

Thanks I'll try to continue to do it myself also but this seems very difficult to me (I have spent hours on this problem...). I just succeed to use the example to "skip" the #import statement.

---

## Comment 6

> Username: malickf  
> Created at: 2017-09-25 10:20:21 UTC  
> Updated at: 2017-09-25 10:21:12 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-331839447  

Is there a way to call methods from the class https://github.com/boostorg/wave/blob/6a7af0e878c2eefa2a68535fdd729146893fdcc6/include/boost/wave/util/cpp_iterator.hpp#L235  inside https://github.com/boostorg/wave/blob/b23bad3a9944dd950e1b8fa7e06e052b06183cf3/samples/custom_directives/custom_directives.hpp#L35 ?  
In hooks we usually gets `ContextT` but I don't know how to recover a pointer to the parent iterator .   
For instance how can I call .handle_pp_directive (https://github.com/boostorg/wave/blob/6a7af0e878c2eefa2a68535fdd729146893fdcc6/include/boost/wave/util/cpp_iterator.hpp#L1118) from inside the hook `found_unknown_directive`  
Is this possible ?  
Thanks

---

## Comment 7

> Username: hkaiser  
> Created at: 2017-09-26 13:11:13 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-332193193  

The ContextT gives you access to all of that. See https://github.com/boostorg/wave/blob/develop/include/boost/wave/cpp_context.hpp, this may help as well: https://github.com/boostorg/wave/blob/1980705a54f1ae334ffe43452404eeb93b08a0c4/include/boost/wave/cpp_context.hpp#L362-L365.

---

## Comment 8

> Username: malickf  
> Created at: 2017-09-27 18:05:05 UTC  
> Updated at: 2017-09-27 18:06:48 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-332606997  

unfortunately It doesn't work, I cannot compile : asap I call `ctx.pop_iteration_context()`  from outside of the context, I get a lot of errors... I haven't been able to find a single example that call `pop_iteration_context()` from outside of the context .

---

## Comment 9

> Username: malickf  
> Created at: 2017-11-21 14:30:36 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-346043548  

Is there any chance someone can help me with this ? I have also recompiled `idl.re` by modifing somes lines to take into account the import keyword as below :   
  
```  
 Pound PPSpace  ( "import" |"include") PPSpace "<" (any\[\n\r>])+ ">"   
        { BOOST_WAVE_RET(T_PP_HHEADER); }  
  
    Pound PPSpace ( "import" |"include")  PPSpace "\"" (any\[\n\r"])+ "\""   
        { BOOST_WAVE_RET(T_PP_QHEADER); }   
  
    Pound PPSpace ( "import" |"include")  PPSpace  
        { BOOST_WAVE_RET(T_PP_INCLUDE); }   
```  
  
I then obtained a new `idl_re.cpp` but at the end it still doesn't work.   
Thanks

---

## Comment 10

> Username: hkaiser  
> Created at: 2017-11-21 15:07:37 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-346055060  

I don't understand why this does not work. Note however, that there are two `.re` files, you might have to change both of them in order to be sure your modifications are picked up.

---

## Comment 11

> Username: malickf  
> Created at: 2017-11-22 14:19:45 UTC  
> Url: https://github.com/boostorg/wave/issues/16#issuecomment-346363106  

Thanks ! I finally succeeded :-) You were right, I recompiled the second .re file and now it's working. Many thanks.
