# #146 - B2: `<visibility>hidden` as a requirement [Closed]

> Username: JBouwer  
> Created at: 2023-11-09 13:33:08 UTC  
> Updated at: 2024-01-20 08:06:11 UTC  
> Closed at: 2024-01-20 08:03:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/146  

From [build/Jamfile.v2](https://github.com/boostorg/stacktrace/blob/4f2da0e653f93a2f6398b7f816f667bbe9699db9/build/Jamfile.v2#L14C14-L14C14):  
```  
project  
  : source-location .  
  : requirements  
    [ requires cxx11_rvalue_references ]  
    <visibility>hidden  
  ;  
```  
  
### Is there a reason why `<visibility>hidden` is a _requirement_?  
i.e. Won't `default-build` be more suitable?  
```  
project  
  : source-location .  
  : requirements  
    [ requires cxx11_rvalue_references ]  
  : default-build  
    <visibility>hidden  
  ;  
```  
  
Thus allowing linking to the various libraries via b2;  
E.g.  
```  
lib MyTestingLib  
    : # Sources  
      ...  
      /boost/stacktrace//boost_stacktrace_noop/<link>static/<visibility>global  
      ...  
    : # Requirements  
      ...  
    : # Default Build  
        <link>shared  
    : # Usage Requirements  
        <define>BOOST_STACKTRACE_LINK  
        <link>shared:<define>BOOST_STACKTRACE_DYN_LINK  
    ;  
  
```  
Currently a target like the above fail to link with "`Undefined symbols ...`".

---

## Comment 1

> Username: JBouwer  
> Created at: 2023-11-10 02:08:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/146#issuecomment-1804966213  

A workaround to achieve the above can be to:  
  
1. Create a static library from the desired StackTrace backend library.  
2.  Link _that_ into the destination library:  
  
i.e.  
```  
# Intermediary Static Library  
lib Boost_StackTrace  
    : # Sources  
        Boost_StackTrace.cpp  
          
        # /boost/stacktrace//boost_stacktrace_noop  
        /boost/stacktrace//boost_stacktrace_basic  
        # ...  
    : # Build Requirements  
        <link>static  
    : # Default Build  
    : # Usage Requirements  
    ;  
  
# Destination Dynamic Library  
lib MyTestingLib  
    : # Sources  
      ...  
      Boost_StackTrace  
      ...  
    : # Requirements  
      ...  
    : # Default Build  
        <link>shared  
    : # Usage Requirements  
        <link>shared:<define>BOOST_STACKTRACE_LINK  
        <link>shared:<define>BOOST_STACKTRACE_DYN_LINK  
    ;  
  
```  
and with `Boost_StackTrace.cpp` being just:  
```  
#include <boost/stacktrace.hpp>  
```

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-01-20 08:06:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/146#issuecomment-1901911863  

Many thanks for the bugreport and fix hints!
