# #149 - Setting color_output=no does not disable the output of color format codes [Closed]

> Username: Markus87  
> Created at: 2018-08-21 07:51:41 UTC  
> Updated at: 2018-09-03 19:57:43 UTC  
> Closed at: 2018-09-03 19:57:43 UTC  
> Url: https://github.com/boostorg/test/issues/149  

I think --color_output=no should not just use the default color for everything, it should stop color format codes from being outputted completely. The current behaviour is very misleading to me.  
https://github.com/boostorg/test/blob/35c9acf5bd38d625ebd45cf940c5b9a19aa2295b/include/boost/test/utils/setcolor.hpp#L304  
  
To understand why it is a problem let me describe my test setup:  
I use ctest to run boost tests and instructed ctest to output everything to a file.  
The file contains all color codes that boost writes, --color_output=yes/no only changes if its all the same color or not, the codes remain any way.

---

## Comment 1

> Username: toonetown  
> Created at: 2018-08-30 18:33:47 UTC  
> Url: https://github.com/boostorg/test/issues/149#issuecomment-417422867  

Agreed.  This also happens when trying to run unit tests within Xcode's debugger windows (which doesn't support ANSI colors).  
  
I was able to work around it (in my particular case) by tweaking the `#define BOOST_TEST_SCOPE_SETCOLOR`.  I added these lines before including my main unit test headers:  
  
```  
#include <boost/test/utils/setcolor.hpp>  
#undef BOOST_TEST_SCOPE_SETCOLOR  
#define BOOST_TEST_SCOPE_SETCOLOR(is_color_output, os, attr, color)  
```  
  
That basically just disables ansi color altogether.

---

## Comment 2

> Username: toonetown  
> Created at: 2018-08-30 18:34:24 UTC  
> Url: https://github.com/boostorg/test/issues/149#issuecomment-417423076  

IMO, I think that if you say `--enable_color=no`, it should completely skip the ansi codes altogether.

---

## Comment 3

> Username: toonetown  
> Created at: 2018-08-30 19:42:52 UTC  
> Url: https://github.com/boostorg/test/issues/149#issuecomment-417443473  

A proposed patch can be found at https://github.com/boostorg/test/pull/154

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-09-03 19:57:43 UTC  
> Url: https://github.com/boostorg/test/issues/149#issuecomment-418185133  

Closed through PR #154
