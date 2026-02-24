# #137 - Why use parent_ in parent_class_of? [Closed]

> Username: ldionne  
> Created at: 2017-04-17 03:50:32 UTC  
> Updated at: 2017-04-22 20:01:19 UTC  
> Closed at: 2017-04-22 19:15:12 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/137  

To me, `parent` makes it look like inheritance. Why not simply `class_of` or something like that? Also, if there's a reason for the current name, I think adding a short explicative note in the documentation would be nice.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-04-17 13:14:13 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/137#issuecomment-294483390  

+1, I also think that the simple `class_of` makes more sense.

---

## Comment 2

> Username: badair  
> Created at: 2017-04-18 00:57:37 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/137#issuecomment-294639887  

Agreed; will fix.

---

## Comment 3

> Username: badair  
> Created at: 2017-04-22 19:15:12 UTC  
> Updated at: 2017-04-22 20:01:19 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/137#issuecomment-296395204  

fixed via d6a6e6cdddce4a2336ddec823bf320ae6294abb1
