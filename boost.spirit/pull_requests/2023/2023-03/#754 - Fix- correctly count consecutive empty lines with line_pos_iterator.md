# #754 Fix: correctly count consecutive empty lines with line_pos_iterator [Closed]

> Username: CKohlert  
> Created at: 2023-03-01 07:31:18 UTC  
> Updated at: 2023-03-08 18:49:40 UTC  
> Closed at: 2023-03-08 18:49:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/754  

Bugfix for: line_pos_iterator in spirit loses count of some lines when they are multiple consecutive empty lines and the newline-type is `\r\n` or `\n\r`

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2023-03-01 15:53:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/754#pullrequestreview-1320139063  

📁 test/support/regression_line_pos_iterator.cpp

```diff
 153 |+         (5,1,"")(5,1,"")
 154 |+         (6,1,"")(6,1,"")
 155 |+         (7,1,"")(7,1,"bar")(7,2,"bar")(7,3,"bar");
```

> Username: Kojoley  
> Created_at: 2023-03-01 15:53:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/754#discussion_r1121957658  

This doesn't look right to me. Am I missing something?

> Username: CKohlert  
> Created_at: 2023-03-01 18:54:39 UTC  
> Updated_at: 2023-03-01 18:54:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/754#discussion_r1122177631  

well, at least the line got counted.   
the position repeats itself, that is a bit strange, but i can look into it...

> Username: Kojoley  
> Created_at: 2023-03-07 20:52:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/754#discussion_r1128573506  

Nevermind, that's happening because of iterator is inside multichar newline


---

## Comment 2

> Username: Kojoley  
> Created_at: 2023-03-07 20:53:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/754#issuecomment-1458860629  

Could you please check if #755 fixes things for you?

---
