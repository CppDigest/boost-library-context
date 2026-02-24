# #69 - Overzealous escaping [Closed]

> Username: evanlenz  
> Created at: 2021-05-21 06:27:43 UTC  
> Updated at: 2021-07-15 07:56:02 UTC  
> Closed at: 2021-07-15 07:56:02 UTC  
> Url: https://github.com/boostorg/docca/issues/69  

(in Quickbook). Fix is in progress. See https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/ref/boost__json__object/operator_lb__rb_.html  
  
```  
operator\[\](  
```  
  
Might be due to a recent regression.  
  
From Vinnie:  
"I think the rule is, you need to escape unless you are in a code block (backticks)  
or the triple backtick delimiter"

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-06-04 19:54:25 UTC  
> Url: https://github.com/boostorg/docca/issues/69#issuecomment-854966133  

I found another broken test case: brackets in ref links. That problem has been around for a longer time. The one cited in this issue (brackets in code blocks) is due to a recent regression (in 407fa7163113cd8f52140f9b572c982651382ce9). They are two separate issues with two different fixes, but they're so closely related that I'll keep both of them in the same test file and fix both under the same issue.
