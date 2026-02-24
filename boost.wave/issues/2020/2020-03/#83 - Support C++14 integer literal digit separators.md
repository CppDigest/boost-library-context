# #83 - [LOW] Support C++14 integer literal digit separators [Open]

> Username: jefftrull  
> Created at: 2020-03-24 18:02:39 UTC  
> Updated at: 2025-10-27 16:51:22 UTC  
> Url: https://github.com/boostorg/wave/issues/83  

Filing this chiefly for the record. cppreference states that since C++14:  
  
> Optional single quotes(') may be inserted between the digits as a separator. They are ignored by the compiler.  
  
I verified this is true in gcc. At this time Wave does not understand them. For example:  
```c++  
#include <iostream>  
  
// can insert single quotes between digits starting in C++14:  
int foo = 1'2'3;   // wave thinks this is two ints and one char literal  
int bar = 0x2'3;   // wave thinks this is two ints and a single quote  
  
int main() {  
    std::cout << foo << "\n";   // 123  
    std::cout << bar << "\n";   // 35  
  
    return 0;  
}  
```  
I don't need this functionality but wanted to record it for some future maintainer.

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-03-24 18:04:53 UTC  
> Url: https://github.com/boostorg/wave/issues/83#issuecomment-603415411  

A test case to demonstrate that Wave breaks these literals up into separate tokens  
[testcase.cpp](https://github.com/boostorg/wave/files/4376935/testcase.cpp.txt)

---

## Comment 2

> Username: hkaiser  
> Created at: 2020-03-24 18:55:02 UTC  
> Url: https://github.com/boostorg/wave/issues/83#issuecomment-603444497  

This shouldn't be too hard to add to the lexers, I think.

---

## Comment 3

> Username: jefftrull  
> Created at: 2020-10-21 05:07:07 UTC  
> Url: https://github.com/boostorg/wave/issues/83#issuecomment-713306116  

The lexers won't be too hard, as they seem to treat everything as a string. The grammars that use them, on the other hand, may need some work - like [here](https://github.com/boostorg/wave/blob/c9480b135c6d3968009b2ba8709f390d66743282/include/boost/wave/grammars/cpp_intlit_grammar.hpp#L118), where a Spirit integer parser is invoked.

---

## Comment 4

> Username: jefftrull  
> Created at: 2025-10-27 16:51:22 UTC  
> Url: https://github.com/boostorg/wave/issues/83#issuecomment-3452339379  

Reviewing this in light of #242 I do think we are still missing the part where we turn numbers with separators into actual values we can use in expressions. The wave driver fires errors on numbers containing digit separators, perhaps due to the literal parsers I mentioned above. So I'm keeping this open for now, but we are a lot closer.
