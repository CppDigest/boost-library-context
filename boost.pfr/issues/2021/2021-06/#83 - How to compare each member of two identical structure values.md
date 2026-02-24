# #83 - How to compare each member of two identical structure values? [Closed]

> Username: SpriteOvO  
> Created at: 2021-06-09 20:15:16 UTC  
> Updated at: 2021-06-09 20:17:56 UTC  
> Closed at: 2021-06-09 20:17:55 UTC  
> Url: https://github.com/boostorg/pfr/issues/83  

I'm writing a Settings feature for my program. After the members are modified at runtime and committed, I need to compare them and call the member handler for each modified member.  
  
A simplified short example: https://godbolt.org/z/Y7PKbrqe5  
  
`pfr::get` only accepts an index as a non-type template parameter, so I obviously can't enumerate at runtime with `fields_count`.  
I didn't find a solution from the docs, am I missing something?  
  
Any help would be appreciated.

---

## Comment 1

> Username: SpriteOvO  
> Created at: 2021-06-09 20:17:55 UTC  
> Url: https://github.com/boostorg/pfr/issues/83#issuecomment-858072498  

Oh, I found an existing issue #69, sorry to open this new one.
