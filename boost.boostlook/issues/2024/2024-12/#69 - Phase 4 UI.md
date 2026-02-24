# #69 - Phase 4 UI [Closed]

> Username: rbbeeston  
> Created at: 2024-12-17 23:48:48 UTC  
> Updated at: 2025-01-17 18:00:35 UTC  
> Closed at: 2025-01-17 18:00:35 UTC  
> Url: https://github.com/boostorg/boostlook/issues/69  

The specific goal for this phase has several components:  
  
1. we want to refactor **font** related tags and classes into single tag level classes that apply the same everywhere, for example taking:  
  
```  
.boostlook h4 {  
    font-size: 130%;  
}  
  
.boostlook .antora h4 {  
     font-size: 125%;  
    font-weight: bold;  
     margin-left: 1rem;  
}  
```  
  
should be :  
  
```  
.boostlook h4 {  
    font-size: 130%;  
    font-weight: bold;  
}  
  
.boostlook .antora h4 {  
     margin-left: 1rem;  
}  
```  
move the font related elements together and the specifics on formatting for a specific case separately. This will simplify the code and insure that fonts will display the same under any doc type.  
  
2. Any references to font weights should be number values based on the variable fonts, so I'd like to start with 500 and go bolder at a +50 increments  
  
4. Be sure all border-radius attributes are removed or set to zero.

---

## Comment 1

> Username: julioest  
> Created at: 2025-01-16 22:32:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/69#issuecomment-2597041697  

@cdw9 feel free to close this once you're finished. Thanks!
