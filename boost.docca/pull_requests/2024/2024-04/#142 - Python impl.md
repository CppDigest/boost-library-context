# #142 Python impl [Merged]

> Username: grisumbras  
> Created at: 2024-04-23 15:05:37 UTC  
> Updated at: 2024-05-03 13:51:17 UTC  
> Merged at: 2024-05-03 10:03:35 UTC  
> Closed at: 2024-05-03 10:03:35 UTC  
> Url: https://github.com/boostorg/docca/pull/142  

Python implementation. Collects data, then outputs using a Jinja template.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-04-23 15:15:27 UTC  
> Url: https://github.com/boostorg/docca/pull/142#issuecomment-2072655745  

An automated preview of the documentation is available at [https://142.docca.prtest.cppalliance.org/tools/docca/example/html/index.html](https://142.docca.prtest.cppalliance.org/tools/docca/example/html/index.html)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2024-04-23 22:33:03 UTC  
> Url: https://github.com/boostorg/docca/pull/142#issuecomment-2073585319  

You have write access, so I would suggest merging it. But if you could squash it all down to one commit it would make more sense. Although if you don't squash it, that is also OK. As long as this doesn't break anything.

---

## Comment 3

> Username: grisumbras  
> Created_at: 2024-04-24 13:22:43 UTC  
> Url: https://github.com/boostorg/docca/pull/142#issuecomment-2074940552  

I found a few issues. Once they've been ironed out, I'll squash and merge.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2024-04-24 15:30:27 UTC  
> Url: https://github.com/boostorg/docca/pull/142#issuecomment-2075223337  

Since you are only adding things, there's no harm to squashing and merging now, and fixing later. If this will help you (by having the script available in the main repository so that other people can start using it sooner) then feel free to merge before fixing. Or not, up to you.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2024-05-03 13:51:16 UTC  
> Url: https://github.com/boostorg/docca/pull/142#issuecomment-2093065935  

Current differences:  
  
Improvements:  
* noexcept support;  
* explicit support;  
* ref qualifier support;  
* hyperlinked types in declarations;  
* old implementation had some extra whitespace in code blocks;  
* underlying types of enumerations are shown;  
* fixed array references in function parameters;  
* fixed grouping bug (it resulted in some functions documented several times);  
* scoped enums are declared as 'enum class';  
* defaulted functions are marked as such.  
  
Minor differences:  
* type declarations end with a semicolon similar to function declarations;  
* instead of NBSP character a UTF16 escape representing NBSP is output;  
* order of operators changes in the summary (my implementation sorts alphabetically, I don't understand the old logic);  
* overload index follows overload order in the summary (affects links);  
* different style of displaying bases.  
  
Major differences:  
* aliases aren't unwrapped;  
* doesn't merge member functions and friends (affects links).

---
