# #91 - safe_numerics_actions::undefined_behavior is fishy [Closed]

> Username: akrzemi1  
> Created at: 2020-10-14 14:14:07 UTC  
> Updated at: 2021-05-23 23:15:38 UTC  
> Closed at: 2021-05-23 22:12:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/91  

It appears to me that the "category" of errors represented by  `safe_numerics_actions::undefined_behavior` and by `EP::on_undefined_behavior()` is never actually used in the library.  
  
There are operations on `int`s that cause UB:  
* overflow,  
* division by zero,  
* the reading of indeterminate value,  
But the library uses different categories for these. So there is nothing left for category `undefined_behavior`.  
  
(I know it from studying the implementation, because the documentation doesn't help in determining this.)  
  
On a different topic, I would expect that overflow and division by zero would be two different categories that I can customize separately in the exception policy. They are fundamentally different: one is a consequence of arithmetic rules, the other is the consequence of implementation limits on machines.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-11-07 17:54:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/91#issuecomment-723474893  

I'm sure that when I defined the category "undefined behavior" it was to catch anything that didn't fit in any of the other categories.  But by the time I was done no such behavior appeared to this category wasn't actually used.  I think leaving it there is not a bad idea - because what does the future hold?  
  
As I was refining the error classification setup - I came upon the then recent efforts to implement error_codes, error_categories etc.  I thought it would be a good idea to follow this framework as I knew that much effort had been invested in it to rationalize and regularize these things.  Since then, these have been added to the standard. And since THEN it seems that there are complaints about it.  So I'm not really sure what to do with it now.  In any case, surely I should update the documentation to explain these categories and perhaps explain what value they might have.  But to do that I have to go back and really understand this system in a better way.  
  
For now, I'll leave this as it is. But we can leave the issue as open - indefinitely if need be.

---

## Comment 2

> Username: johnmcfarlane  
> Created at: 2021-05-23 22:35:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/91#issuecomment-846634623  

For reference, the approach adopted by [CNL](https://github.com/johnmcfarlane/cnl/)'s `overflow_integer` is that UB isn't a category of error, but rather  
  
1. one consequence of certain errors from operations on native types, and  
2. (optionally) the chosen consequence for all applicable(*) errors from operations on the 'safe' library type.  
  
(*) by applicable, I mean range errors in _CNL_'s case and all arithmetic errors in _safe_numerics_'s case, including range errors, uninitialised values and divide-by-zero.

---

## Comment 3

> Username: robertramey  
> Created at: 2021-05-23 23:15:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/91#issuecomment-846640253  

I defined the category "undefined behavior".  But it turns out that when I assigned all errors detected to categories, non of them ended up in the category "undefined behavior".  This is unforeseen but makes a lot of sense in hindsight.  Since we're trapping and handling every possible outcome, there is no "undefined behavior" any more.  I'm not sure how that maps to your system, but perhaps mine makes more sense now.
