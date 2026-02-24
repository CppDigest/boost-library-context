# #17 - Error C4596 'proto_extends': illegal qualified name in member declaration [Open]

> Username: friente  
> Created at: 2019-08-16 06:43:03 UTC  
> Updated at: 2019-08-21 13:32:12 UTC  
> Url: https://github.com/boostorg/proto/issues/17  

Hello,  
  
I have built boost filesystem and system on windows msvc 2017 and then I have included the boost include dir in my project to build my application.  
When I build the solution on msvc 2017 15.9.15 I get the following error:  
  
`Error C4596 'proto_extends': illegal qualified name in member declaration fiction e:\development\boost-1.70.0\include\boost\xpressive\regex_primitives.hpp 569 `  
  
Which is related to the macro defined in extends.h  
  
```  
    #define BOOST_PROTO_EXTENDS_USING_ASSIGN_NON_DEPENDENT(Derived)                                 \  
        typedef Derived::proto_extends proto_extends;                                               \  
        using proto_extends::operator =;                                                            \  
        BOOST_PROTO_EXTENDS_COPY_ASSIGN_(Derived, BOOST_PP_EMPTY)                                   \  
        /**/  
```  
  
I have also tried adding Hint files in the solution but it doesn't work.  
Is there an error in the typedef?   
  
Thanks for your support.
