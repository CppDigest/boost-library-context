# #156 - the matching result of boost regex match algorithm is different from "grep -P" and pcre2 [Closed]

> Username: xayahion  
> Created at: 2021-09-28 07:56:30 UTC  
> Updated at: 2021-11-01 01:58:19 UTC  
> Closed at: 2021-11-01 01:58:19 UTC  
> Url: https://github.com/boostorg/regex/issues/156  

three regex match algorithm results is following:  
the first pattern string is "(a((?0)(  ))?+)?+(*SKIP) ?(?1)*?".  
By replacing "?1" with "(a((?0)(  ))?+)", we get the second pattern string that is "(a((?0)(  ))?+)?+(*SKIP) ?((a((?0)(  ))?+))*?"  
the text string both is " aa " ，space before and after "aa".  
  
1.boost  
(a((?0)(  ))?+)?+(*SKIP) ?(?1)*?  
 aa   
result: assert failed （https://github.com/boostorg/regex/issues/152）  
  
(a((?0)(  ))?+)?+(*SKIP) ?((a((?0)(  ))?+))*?  
result: not matched  
  
2.pcre2  
(a((?0)(  ))?+)?+(*SKIP) ?(?1)*?  
 aa   
result: matched  
  
(a((?0)(  ))?+)?+(*SKIP) ?((a((?0)(  ))?+))*?  
result: matched  
  
3.grep -P  
/root/a.txt include " aa "  
grep -P "(a((?0)(  ))?+)?+(*SKIP) ?(?1)*?" /root/a.txt   
result: matched  
  
grep -P "(a((?0)(  ))?+)?+(*SKIP) ?((a((?0)(  ))?+))*?" /root/a.txt  
result: matched  
  
According to three results，pcre2 and "grep -P" can match, so is there a problem with boost regex match algorithm? who knows that , i do not know much about the boost regex.   
thanks~

---

## Comment 1

> Username: xayahion  
> Created at: 2021-11-01 01:58:13 UTC  
> Url: https://github.com/boostorg/regex/issues/156#issuecomment-955860729  

fixed by #165   
closed
