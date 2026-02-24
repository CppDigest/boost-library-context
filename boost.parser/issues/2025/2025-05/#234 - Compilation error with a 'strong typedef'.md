# #234 - Compilation error with a 'strong typedef' [Open]

> Username: psiha  
> Created at: 2025-05-05 17:16:09 UTC  
> Updated at: 2025-07-12 20:45:08 UTC  
> Url: https://github.com/boostorg/parser/issues/234  

MRE https://godbolt.org/z/fE1ceqehP  
  
To make it compile, either/at least one of:  
- switch boost::container::vector back to std::vector (it has to be exactly that, no other container will work)  
- replace the strong typedef with a weak one  
- simplify the parser to just ( bp::short_ % ',' ).

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-12 20:44:32 UTC  
> Updated at: 2025-07-12 20:45:08 UTC  
> Url: https://github.com/boostorg/parser/issues/234#issuecomment-3066035498  

Do you have a proposed fix?  Looking at those errors, it looks like you're running afoul of language rules, not library problems.  If you make a typedef instead, things work -- as you noted.  I don't know of a way to test whether a destructuring will be well-formed without using something like statement expressions (which I think we'll get in C++29, but don't have now).  
  
My current thinking is that this should just be mentioned in the docs.
