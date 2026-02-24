# #1201 - option to disable utf8-validation [Open]

> Username: vinniefalco  
> Created at: 2018-07-21 00:51:57 UTC  
> Updated at: 2022-06-19 14:55:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1201  

This would be even faster than the currently optimized version of `utf8_checker`.

---

## Comment 1

> Username: carolinebeltran  
> Created at: 2018-07-22 16:55:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1201#issuecomment-406881181  

Hi Vinnie, is this a feature that you are thinking of adding to Beast?  If so, I think that it would be a good idea, for instance, I use ICU for UTF-8 validation, comparison, transformation and any manipulation required such as word boundaries, etc.  I see no point in redundant validation for users who may use other Unicode libraries.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-07-22 17:33:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1201#issuecomment-406883593  

Yes this is a planned feature. Note, you can always use binary mode for messages, no checking is performed then.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-06-18 20:58:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1201#issuecomment-1159561278  

Would that be another   
  
```cpp  
void  
set_option(utf8_option const& opt)  
```  
  
?  
  
Do we want the logic in `stream`, checking if we should ignore the checker, or in `utf8_checker`, with some internal logic to disable it?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-19 14:55:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1201#issuecomment-1159748116  

```  
void set_validate_utf8( bool v );  
bool validate_utf8() const noexcept;  
```  
  
logic in `stream`
