# #213 Add unicode symbols parser [Merged]

> Username: necessarily-equal  
> Created at: 2025-02-07 06:40:41 UTC  
> Updated at: 2025-02-21 05:54:23 UTC  
> Merged at: 2025-02-21 05:51:17 UTC  
> Closed at: 2025-02-21 05:51:18 UTC  
> Url: https://github.com/boostorg/parser/pull/213  

Hey :)  
I added a parser to match characters from the unicode symbols category. Here is the PR  
  
I'm using it for my own parser, but I could see it used to parse some [Haskell](https://www.haskell.org/onlinereport/haskell2010/haskellch2.html#x7-140002) code for example (ctrl-F symbol in that document). The haskell definition of a symbol is a bit different for ascii characters, but it would still be possible to construct it:  
  
```c++  
haskell_symbol_def = (bp::symb | bp::punct) - (bp::char_('"') | '\'' | '(' | ')' | ',' | ';' | '[' | ']' | '_' | '`' | '{' | '}');  
```  
  
Here in this PR I've updated the documentation manually. Feel free to regenerate it properly to ensure correctness. You may also want to reindent the list of characters if you care about aesthetics enough.

---

## Comment 1

> Username: necessarily-equal  
> Created_at: 2025-02-07 06:41:57 UTC  
> Url: https://github.com/boostorg/parser/pull/213#issuecomment-2642068978  

About the naming of the new parser: I've used "symb" to avoid conflicting with "symbols" (lookup tables). I think that's reasonable since "punct" is also abbreviated.

---

## Comment 2

> Username: tzlaine  
> Created_at: 2025-02-16 19:24:06 UTC  
> Url: https://github.com/boostorg/parser/pull/213#issuecomment-2661579540  

This look really useful, and the code looks good as well.  A couple of nits: 1) please don't do drive-by fixes like the typo correction in the same commit as a semantic change, and 2) please don't touch parser_reference.xml at all, as it is generated.  
  
A bigger concern, though, is that there are no tests.  If you can update the character set test with this new charset (and please include a test that `char_set<symb_chars>::chars` is sorted), and address the nits above, this will be good to merge.

---

## Comment 3

> Username: necessarily-equal  
> Created_at: 2025-02-17 12:40:17 UTC  
> Url: https://github.com/boostorg/parser/pull/213#issuecomment-2663004572  

> 1) please don't do drive-by fixes like the typo correction in the same commit as a semantic change  
  
I think this is already correct  
  
> 2) please don't touch parser_reference.xml at all, as it is generated.  
  
Okay, removed that change  
  
> A bigger concern, though, is that there are no tests. If you can update the character set test with this new charset  
  
Done, hopefully that is enough  
  
> (and please include a test that char_set<symb_chars>::chars is sorted), and address the nits above, this will be good to merge.  
  
Added as well, this one is new, I didn't exist yet when I wrote the code :D  
  
This should be good to go. Not sure why the drone CI did fail with a segfault last time. Since at the time I didn't have any entry point for `symb` I assume there is a problem somewhere else

---

## Comment 4

> Username: tzlaine  
> Created_at: 2025-02-21 05:54:22 UTC  
> Url: https://github.com/boostorg/parser/pull/213#issuecomment-2673538724  

> > 1. please don't do drive-by fixes like the typo correction in the same commit as a semantic change  
>   
> I think this is already correct  
  
I went ahead and merged this PR, but I think you missed my point.  The comment/doc spelling fix "clases" -> "classes" is definitely needed, but it should have gone in a separate commit.  What if I discovered something wrong with this PR and had to revert it?  I'd then also have to remember to go back and fix "clases" again.  
  
Anyway, thanks for the contribution!  It's much appreciated.

---
