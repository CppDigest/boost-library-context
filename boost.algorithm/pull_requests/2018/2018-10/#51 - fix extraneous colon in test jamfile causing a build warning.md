# #51 fix extraneous colon in test jamfile causing a build warning [Merged]

> Username: jeking3  
> Created at: 2018-10-27 12:13:11 UTC  
> Updated at: 2018-10-30 16:40:13 UTC  
> Merged at: 2018-10-30 16:40:13 UTC  
> Closed at: 2018-10-30 16:40:13 UTC  
> Url: https://github.com/boostorg/algorithm/pull/51  

```  
jking@ubuntu:~/boost/libs/algorithm/test$ ../../../b2 -q  
Jamfile.v2:19: Unescaped special character in argument algorithm:  
```

---

## Comment 1

> Username: mclow  
> Created_at: 2018-10-27 15:30:34 UTC  
> Url: https://github.com/boostorg/algorithm/pull/51#issuecomment-433630416  

Why the quoting?  (I don't have any problem with removing the colons)

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-10-27 17:13:10 UTC  
> Url: https://github.com/boostorg/algorithm/pull/51#issuecomment-433638373  

There is no standard for this, just using what I've seen in libraries I have been in the most.  Examples with and without quotes are abundant.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-10-30 15:31:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/51#issuecomment-434348525  

Changed it to what you were looking for.

---
