# #33 According to RFC3164, the day of month field in the timestamp should … [Merged]

> Username: wordaligned  
> Created at: 2016-10-27 21:59:04 UTC  
> Updated at: 2016-10-27 22:30:17 UTC  
> Merged at: 2016-10-27 22:30:17 UTC  
> Closed at: 2016-10-27 22:30:17 UTC  
> Url: https://github.com/boostorg/log/pull/33  

…be formatted as two characters:  
  
```  
"If the day of the month is less  
 than 10, then it MUST be represented as a space and then the  
 number.  For example, the 7th day of August would be  
 represented as "Aug  7", with two spaces between the "g" and  
 the "7".  
```  
  
The printf formatter "%2d" does this. "% 2d" will prepend an unwanted space, e.g.  
  
```  
 printf("Aug % 2d\n", 27) => Aug  27  
```

---
