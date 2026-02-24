# #34 - private bases should not be displayed [Closed]

> Username: vinniefalco  
> Created at: 2018-12-25 01:12:57 UTC  
> Updated at: 2020-12-15 16:34:17 UTC  
> Closed at: 2020-12-15 16:34:17 UTC  
> Url: https://github.com/boostorg/docca/issues/34  

Synopsis should not include private bases.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-10 01:01:24 UTC  
> Updated at: 2020-11-10 01:02:12 UTC  
> Url: https://github.com/boostorg/docca/issues/34#issuecomment-724380239  

```  
namespace issue_34 {  
  
struct U  
{  
};  
  
/// U should not appear  
struct T : private U  
{  
};  
  
}  
```

---

## Comment 2

> Username: evanlenz  
> Created at: 2020-11-26 08:27:06 UTC  
> Url: https://github.com/boostorg/docca/issues/34#issuecomment-734150709  

Is this unconditionally true, or should private bases be shown when $include-private-members is enabled?

---

## Comment 3

> Username: evanlenz  
> Created at: 2020-11-26 08:34:43 UTC  
> Url: https://github.com/boostorg/docca/issues/34#issuecomment-734154242  

The solution I implemented will always suppress the private base from being displayed. I can change it if that's wrong.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-11-30 19:36:16 UTC  
> Url: https://github.com/boostorg/docca/issues/34#issuecomment-735996655  

I think its fine, and since we don't actually use this anywhere important yet - we don't need a definitive answer right now.
