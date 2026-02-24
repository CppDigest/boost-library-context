# #222 - Failing to emit line directive if included file has a #if or #define on the first line [Open]

> Username: njnobles  
> Created at: 2024-09-20 01:44:04 UTC  
> Updated at: 2024-10-03 04:11:25 UTC  
> Url: https://github.com/boostorg/wave/issues/222  

Wave is not emitting the line directive if the first line in the included file is a `#if` or `#ifdef` that evaluates true and has content in its block. Likewise, it won't emit the line directive if the first line is a `#define`. This issue only occurs if there are not multiple `#define` or `#if` occurring on subsequent lines.  
When using `default_preprocessing_hooks`, the above applies if the `#if`/`#ifdef`/`#define` is the first non-empty line or non-comment line. Essentially, if the directive immediately precedes the first content emitted from the included file. With `eat_whitespace`, the directive must be on the very first line of the include file to exhibit the issue.  
  
Not sure if I've explained it very well, but I think an example will help better demonstrate the issue.  
  
**Example:**  
_a.cpp_  
```  
#include "if.h"  
#include "def.h"  
test  
```  
_if.h_  
```  
#if 1 //This must be on the first line for eat_whitespace, can be on 2nd, 3rd etc, for default_preprocessing_hooks as long as it's whitespace before it  
if_h  
#endif  
```  
_def.h_  
```  
#define A  
def_h  
```  
  
**Resulting preprocessed file:**  
_a.preproc_  
```  
  
if_h  
  
def_h  
```  
  
**Expected output:**  
```  
#line 2 "if.h"  
if_h  
#line 2 "def.h"  
def_h  
```  
  
As mentioned, the line directives do get emitted correctly if the include file is something like this:  
```  
#define A  
#define B  
content  
```  
So it's only occurring when there's a single directive at the beginning.

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-10-02 05:28:47 UTC  
> Url: https://github.com/boostorg/wave/issues/222#issuecomment-2387660139  

Thank you for finding this excellent corner case!  
  
I'm still exploring the issue but I find it's actually more general: _any_ blank line (or in your case, a directive that becomes a blank line) will cause the line directive to be suppressed. For example:  
  
a.cpp  
```  
#include "b.h"  
```  
b.h  
```  
  
B  
```  
Running Wave on a.cpp produces:  
```  
  
B  
```  
i.e., no line directive showing that `B` came from `b.h` appears.  
  
I think this is because the logic that determines whether to emit a line directive checks only whether the number of newlines (as perceived by the reader of the preprocessed code) is correct, and not whether the filename has changed.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-10-03 04:11:24 UTC  
> Url: https://github.com/boostorg/wave/issues/222#issuecomment-2390478305  

Confirmed! Thanks for the report.
