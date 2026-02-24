# #398 - Coverity static analysis defects [Closed]

> Username: mloskot  
> Created at: 2018-09-21 08:54:21 UTC  
> Updated at: 2025-05-10 01:42:12 UTC  
> Closed at: 2025-05-10 01:42:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/398  

(copied from https://svn.boost.org/trac10/ticket/13331, only Spirit's parts from the attached report)  
  
Boost 1.57.0  
  
A static analysis tool called Coverity found medium and high defects in the boost source code. See attached file for defect type, defect category, filename and line number of defect.  
  
  
Defect   Type | Defect Category | Line Number | Filename  
-- | -- | -- | --  
Resource   leak | Resource leaks | 271 | /boost/include/boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp  
Wrapper   object use after free | Memory - illegal access | 167 | /boost/include/boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-09-21 12:39:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/398#issuecomment-423517055  

Thanks for reporting.  
  
Unfortunately, I see no potential problems on these lines ([grammar.ipp#L271](https://github.com/boostorg/spirit/blame/60c971df52ee06932c8344be5dbaecd1219a69fa/include/boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp#L271), [grammar.ipp#L167](https://github.com/boostorg/spirit/blame/60c971df52ee06932c8344be5dbaecd1219a69fa/include/boost/spirit/home/classic/core/non_terminal/impl/grammar.ipp#L167)).

---

## Comment 2

> Username: mloskot  
> Created at: 2018-09-21 13:05:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/398#issuecomment-423524232  

@Kojoley My aim was to clean some Trac issues and I did not want to close this particular one without letting the Spirit developers know (on Trac, it was assigned to `None` component, left unnoticed). So, if there actually is no issue, I'd close this. FYI, the Trac ticket has been closed.

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-09-21 13:14:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/398#issuecomment-423526661  

I understand, thanks again, let leave the issue opened as I might be investigating these warnings in the future.
