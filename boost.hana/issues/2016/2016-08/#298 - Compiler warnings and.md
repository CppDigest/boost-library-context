# #298 - Compiler warnings [-Wshadow] and  [-Wdocumentation] [Closed]

> Username: DominikDeak  
> Created at: 2016-08-05 02:20:51 UTC  
> Updated at: 2016-12-05 16:41:29 UTC  
> Closed at: 2016-12-05 16:41:29 UTC  
> Url: https://github.com/boostorg/hana/issues/298  

Xcode 8 beta 4 emits these messages with warnings cranked up:  
  
```  
boost/include/boost/hana/ext/std/integer_sequence.hpp:108:25: warning: declaration shadows a variable in namespace 'boost::hana' [-Wshadow]  
boost/include/boost/hana/filter.hpp:88:47: warning: declaration shadows a static data member of 'filter_indices<b...>' [-Wshadow]  
boost/include/boost/hana/map.hpp:248:28: warning: declaration shadows a variable in namespace 'boost::hana' [-Wshadow]  
boost/include/boost/hana/fwd/integral_constant.hpp:251:10: warning: '@tparam' command used in a comment that is not attached to a template declaration [-Wdocumentation]  
boost/include/boost/hana/fwd/integral_constant.hpp:255:10: warning: '@tparam' command used in a comment that is not attached to a template declaration [-Wdocumentation]  
boost/include/boost/hana/pair.hpp:49:37: warning: declaration shadows a variable in namespace 'boost::hana' [-Wshadow]  
boost/include/boost/hana/pair.hpp:49:58: warning: declaration shadows a variable in namespace 'boost::hana' [-Wshadow]  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2016-08-06 18:48:32 UTC  
> Url: https://github.com/boostorg/hana/issues/298#issuecomment-238040985  

Thanks for the report. Certainly I can't do much to fix the documentation warning, since it's wrong. The `@tparam` is documenting a variable template, so a warning should not be emitted.  
  
The shadowed variable warning is also unreasonable IMO.. I'll reproduce on my end and see what I want to do with it.  
  
FWIW, if that's the issue, you can probably get rid of these warnings generated from Hana in your own code by include Hana with `<>` instead of `""`. The compiler should get that they are system headers and thus not generate warnings in there.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-08-06 18:56:43 UTC  
> Url: https://github.com/boostorg/hana/issues/298#issuecomment-238043027  

Note that these warnings do not only appear when compiling with Xcode 8; Xcode 7 also generates them. Also, there are many more instances of these warnings than what you reported above. I don't think fixing all of them is reasonable and adds much value to the library itself unless it makes the life of users much easier. What's your situation? Is it easy for you to avoid these warnings (e.g. include Hana's headers properly), or is it difficult (e.g. using pragmas to silence warnings is necessary)?

---

## Comment 3

> Username: DominikDeak  
> Created at: 2016-08-07 06:57:34 UTC  
> Updated at: 2016-08-07 07:04:13 UTC  
> Url: https://github.com/boostorg/hana/issues/298#issuecomment-238067309  

Thanks for looking into this.  
  
> FWIW, if that's the issue, you can probably get rid of these warnings generated from Hana in your own code by include Hana with <> instead of "".  
  
I am including them with <> and that doesn't seem to make a difference.   
  
> What's your situation? Is it easy for you to avoid these warnings (e.g. include Hana's headers properly), or is it difficult (e.g. using pragmas to silence warnings is necessary)?  
  
I got clang running with `-Wextra -Wall`, so it will warn about a lot of pedantic things. I can  live without `-Wdocumentation`, so I can turn that off. However, `-Wshadow` is something I need. I could certainly use pragmas around #include to suppress the warnings:  
  
``` cpp  
#pragma clang diagnostic push  
#pragma clang diagnostic ignored "-Wshadow"  
  
#include <boost/hana/...>  
  
#pragma clang diagnostic pop  
```  
  
It's not ideal, of course. Perhaps there is a better way, but I haven't figured out to how to make clang suppress all warnings for third party libs in the command line.  
  
Anyway, no biggie, but I thought I should bring this to your attention.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-12-05 16:41:29 UTC  
> Url: https://github.com/boostorg/hana/issues/298#issuecomment-264905248  

The commit above should have fixed it, but please LMK if you still have problems and I'll reopen this. I'm not willing to butcher the code to get rid of these warnings, but if I can do simple changes that do not alter readability while making your like easier, I'm all for it.
