# #717 - X3: Top level documentation improvements [Open]

> Username: Bockeman  
> Created at: 2022-01-26 15:49:21 UTC  
> Updated at: 2025-05-09 23:38:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/717  

Under **Boost Liraries** "Spirit" and "Spirit Classic" are listed, but not distinguished.  
It would be helpful if the latter indicated something like "a historical version, formerly Spirit 1.8".   
There should be an entry for X3 with a user orientated distinction (even if only "for C++14 and later").  
There's a "Spirit Repository" but this is 2.59, and nothing equivalent for X3.  
  
There's now a **link to X3** on the Spirit 2.59 page:  
https://www.boost.org/doc/libs/develop/libs/spirit/doc/html/index.html  
but it is well hidden, right at the bottom of the page.  
But there's no reverse link to Spirit 2.59 on the X3 page  
https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/index.html  
  
The 2.59 Preface covers a **History**, but does not mention X3.  
The X3 Preface does not show any history; this would be extremely useful.  
  
Unlike 2.59, X3 does not contain a **Rationale** section.  
The Rationale (or possibly History) should explain the differences between 2.59 and X3, and the reasoning behind the design choices.  
It would be really helpful if this also contained something like "design objectives" like:  
  
- Intended to be simple and intuitive for casual users, but options available for the most sophisticated tasks.  
- Why X3 does not have the Qi,Karma,Lex equivalents.  
- Claims about performance and similar (really important for _selling_ X3 and getting universal support).  
  
Unlike 2.59, X3 does not contain a **Notes** section which might contain "Porting from 2.59".  
  
The X3 index page  
https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/index.html  
contains a typographical error:  
```  
WARNING*: C++14 compilers support will be dropped soon.  
```  
should be corrected to  
```  
WARNING*: C++14 compiler support will be dropped soon.  
```  
_In English, a noun-adjective (a noun used as an adjective) is always singular._  
  
There's a dangling **s** forming the penultimate line.

---

## Comment 1

> Username: djowel  
> Created at: 2022-01-27 02:50:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/717#issuecomment-1022795722  

It seems this is my task. Thanks for your comments! I'll find some time to work on this. Let's keep this open for now.
