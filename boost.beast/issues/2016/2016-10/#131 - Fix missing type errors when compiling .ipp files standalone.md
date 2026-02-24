# #131 - Fix missing type errors when compiling .ipp files standalone [Closed]

> Username: awhatson  
> Created at: 2016-10-14 02:54:58 UTC  
> Updated at: 2016-10-17 02:07:05 UTC  
> Closed at: 2016-10-17 02:07:05 UTC  
> Url: https://github.com/boostorg/beast/issues/131  

The .ipp files currently produce compilation errors when editing using clang-complete (or similar IDE functionality) due to missing types.  Similar work for the .hpp files was done in #127, however fixing these .ipp files will involve more careful work shuffling type definitions between the .ipp and its related .hpp.  
  
These errors can be found in a rudimentary way using the following process:  
  
<pre>find -name '*.ipp' | while read f; do  
  g=${f/%.ipp/.hpp}  
  cp $f $g  
  g++ -std=c++11 -Wall -Wextra -Wpedantic -Iinclude -Iextras $g  
  rm -f $g $g.gch  
done</pre>

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-14 10:28:45 UTC  
> Updated at: 2016-10-14 10:29:58 UTC  
> Url: https://github.com/boostorg/beast/issues/131#issuecomment-253764999  

It looks like you're trying to compile .ipp files separately. That's probably never going to work - the purpose of .ipp files and the reason they are distinguished from .hpp files is that they require other header material to be included first. They don't stand alone. There are a few reasons why the code is structured this way:  
- Some classes have circular dependencies  
- Function definitions are kept out of the files holding public interfaces  
- The documentation tool doesn't have to parse the additional header material  
- .hpp files with declarations for public APIs are kept down in size  
  
That's why these files appear at the bottom of their corresponding .hpp files:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/core/basic_streambuf.hpp#L301  
  
Also you shouldn't have `-Iextras` on the command line, because the extras directory does not contain public interfaces.

---

## Comment 2

> Username: awhatson  
> Created at: 2016-10-17 02:07:05 UTC  
> Url: https://github.com/boostorg/beast/issues/131#issuecomment-254096273  

Thanks, that's fair.
