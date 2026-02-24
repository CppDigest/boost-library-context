# #24 - Line numbers wrong after conditional section [Closed]

> Username: malickf  
> Created at: 2017-11-29 15:38:45 UTC  
> Updated at: 2022-06-25 14:09:56 UTC  
> Closed at: 2022-06-25 14:09:56 UTC  
> Url: https://github.com/boostorg/wave/issues/24  

I have a small problem and I don't know if this can be easily fixed in wave.  
  
I'm using wave to preprocess a file. It correctly handles macro.   
For instance , output of the following code :   
  
```  
#define DEBUG  
  
MyClass::Test()  
{  
#ifdef DEBUG  
	print("Test");  
#endif  
    this.Test();  
}  
   
```  
is  (B) :   
  
  
```  
#line 3 "C:\.....\myfile.c"  
MyClass::Test()  
{  
	print("Test");  
    this.Test();  
}  
   
```  
Then I apply a home-made linter to the preprocessed file (B) . However, I'm struggling to identify the initial line number from the preprocessed file.   
  
Is there a way to get more information in the preprocessed file, something similar to :   
  
  
```  
#line 3 "...myfile.c"  
MyClass::Test()  
{  
#line 6 "...myfile.c"  
	print("Test");  
#line 8 "...myfile.c"  
    this.Test();  
}  
```  
(or any other methods that permits to find the initial line number when macro is used .  
Don't hesitate to tell me if I'm not clear enough.  
  
Many thanks.

---

## Comment 1

> Username: hkaiser  
> Created at: 2017-11-29 15:54:28 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-347904314  

This looks like a problem in wave. It shouldn't remove the directives without leaving an empty line or adding an additional #line directive.

---

## Comment 2

> Username: malickf  
> Created at: 2017-11-30 10:04:50 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348141056  

Ok, this is definitely a problem.   
The following code :   
```  
  
#define DEBUG  
  
MyClass::Test()  
{  
#ifdef DEBUG  
	print("Test");  
#else  
    print("Test2");  
#endif  
    this.Test();  
}  
  
```  
  
   
produces :   
```  
  
#line 3 "....myfile"  
MyClass::Test()  
{  
	print("Test");  
    this.Test();  
}  
   
```  
  
so there is no way to find the initial line number. I'll try to correct it.

---

## Comment 3

> Username: malickf  
> Created at: 2017-11-30 10:54:27 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348153976  

I believe the following condition  https://github.com/boostorg/wave/blob/cad8f03ada147c5b28b9f472dab3ce02d5beb8e7/include/boost/wave/util/cpp_iterator.hpp#L535 should be true after a macro expansion but I think it is not the case.

---

## Comment 4

> Username: hkaiser  
> Created at: 2017-11-30 13:31:02 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348188380  

Normally, the wave library inserts either a single empty line or a `#line` directive (if more than one line needs to be 'skipped') into the output. The idea is not to clutter the generated output with `#line` directives where possible. This also means, that any solitude preprocessor statement (like the `#if` in your case) should have been replaced by a single empty line in the output. I have not looked what's wrong yet, but I'm very surprised as I'm sure we would have seen this before.

---

## Comment 5

> Username: malickf  
> Created at: 2017-11-30 13:39:45 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348190489  

Hi @hkaiser , I may have miss something then...  It doesn't for me. Could you test the above pseudo-code to confirm that it behave as you think ? Thanks

---

## Comment 6

> Username: hkaiser  
> Created at: 2017-11-30 13:50:46 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348193205  

I can confirm that I see the problem as well.

---

## Comment 7

> Username: malickf  
> Created at: 2017-11-30 13:54:27 UTC  
> Updated at: 2017-11-30 13:54:45 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348194151  

@hkaiser I just realized (thanks to your other answer about preserving comment) that I need to use   
  
   ```  
ctx.set_language(  
               boost::wave::enable_emit_line_directives(ctx.get_language()));  
```  
and then the current problem is solved in the way you say. GREAT !!

---

## Comment 8

> Username: hkaiser  
> Created at: 2017-11-30 13:57:33 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348194972  

This is still a problem requiring investigation. You found a mode where it doesn't happen, but your example should produce the correct answer in any case.

---

## Comment 9

> Username: malickf  
> Created at: 2017-11-30 14:00:13 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348195649  

I'm really impressed by this library, it is quiet difficult to use it and I found the documentation a bit too short (especially about "language_support" and all the options..). But it definitely worth to spend time to understand it because it is by far, the best open source preprocessor library. Many thanks for sharing your knowledge and for your work !

---

## Comment 10

> Username: malickf  
> Created at: 2017-11-30 14:01:12 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348195912  

Ok so I'm living this issue open. At least for the moment I can use this trick.

---

## Comment 11

> Username: malickf  
> Created at: 2017-11-30 14:59:53 UTC  
> Updated at: 2017-11-30 15:01:59 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-348213144  

**UPDATE :**   
  
Unfortunately it doesn't really works : when the macro statement is part of a file that is included, even those if I use :   
```  
ctx.set_language(  
            boost::wave::enable_emit_line_directives(ctx.get_language()));  
```  
  
there is no blank line or emitted line .   
  
  
Example  :   
  
FILE : main.c :  
`  
#include "file_with_macro.h"`  
  
FILE : file_with_macro.h :   
  
   
```  
  
#define DEBUG  
  
MyClass::Test()  
{  
#ifdef DEBUG  
	print("Test");  
#else  
    print("Test2");  
#endif  
    this.Test();  
}  
  
  
```  
The output of the preprocessing apply to main.c produces :   
  
```  
#line 1 "C:/..............\\file_with_macro.h"  
﻿  
  
  
MyClass::Test()  
{  
	print("Test");  
    this.Test();  
}  
  
  
```  
  
So it seems that there is a problem with macro and emitted lines.

---

## Comment 12

> Username: jefftrull  
> Created at: 2022-06-23 19:29:12 UTC  
> Url: https://github.com/boostorg/wave/issues/24#issuecomment-1164788051  

Using the wave tool on the included file alone demonstrates that the line numbers are wrong. The `#else` portion is long enough to require an additional `#line` directive before `this.Test();`, but  we don't emit it.
