# #288 - Namespace macros [Closed]

> Username: viboes  
> Created at: 2016-06-12 01:37:36 UTC  
> Updated at: 2021-10-29 17:20:12 UTC  
> Closed at: 2021-10-29 17:20:12 UTC  
> Url: https://github.com/boostorg/hana/issues/288  

What about changing  
  
```  
BOOST_HANA_NAMESPACE_BEGIN  
```  
  
by  
  
```  
namesapce  BOOST_HANA_NAMESPACE_OPEN {  
```  
  
and  
  
```  
BOOST_HANA_NAMESPACE_END  
```  
  
by  
  
```  
} BOOST_HANA_NAMESPACE_CLOSE  
```  
  
This reduces the syntactical noise of the macros, the braces are pair wised enabling folding in some editors.  
  
``` c++  
namespace  BOOST_HANA_NAMESPACE_OPEN {  
} BOOST_HANA_NAMESPACE_CLOSE  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2016-06-14 02:56:08 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-225766913  

I don't think this reduces the syntactical noise of the macros (quite the contrary, in fact), because you have more symbols to type and you have to think about how the macro interacts with these symbols. The ease of interaction with editors is a valid point. What editor are you using?

---

## Comment 2

> Username: ldionne  
> Created at: 2016-06-14 02:56:56 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-225766998  

But regardless, why would you want to fold a whole namespace, since it basically means folding the whole contents of the file given the structure of Hana's headers?

---

## Comment 3

> Username: viboes  
> Created at: 2016-06-15 16:19:45 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-226240155  

I'm using Eclipse. You are right that most of the headers (if not all) wouldn't profit from unfolding :(   
  
Be free to close it if you don't find an advantage.

---

## Comment 4

> Username: ldionne  
> Created at: 2016-06-16 01:37:03 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-226366554  

All I'm trying to see if whether that provides an advantage to users of some editor(s). For example, I know Sublime Text -- which I use -- wouldn't benefit from it much, but if this simple change is a big plus for many potential users, then it might be worth it to change. Is there an advantage to using what you propose in Eclipse?

---

## Comment 5

> Username: ricejasonf  
> Created at: 2016-06-17 21:32:07 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-226886630  

I think I remember that the macro was put in place for forwards compatibility with the use of inline namespaces or something. If the macro is causing problems, maybe a simple script or refactoring tool could get the job done when the time comes.

---

## Comment 6

> Username: ldionne  
> Created at: 2016-06-29 01:21:27 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-229231010  

The macro is not causing any problem to me, at least. @viboes Is there any advantage to doing what you propose in Eclipse?

---

## Comment 7

> Username: ldionne  
> Created at: 2016-07-06 03:20:23 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-230661733  

Closing for lack of feedback.

---

## Comment 8

> Username: Aster89  
> Created at: 2021-01-15 23:38:07 UTC  
> Updated at: 2021-01-15 23:45:01 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-761255849  

Hello there! @ricejasonf linked this off [my question on stackoverflow](https://stackoverflow.com/questions/65741227/why-does-boost-hana-use-macros-for-opening-closing-namespaces?noredirect=1#comment116240629_65741227), hence I write to give my feedback.  
  
Writing a namespace in the non-macro way, if the editor is able to take advantage of it, allows 3 things:  
  
  - folding, which I really don't care about, honestly;  
  - jumping from opening to closing curly braces;  
  - being with the cursor on the closing/opening curly brace, the corresponding opening/closing curly brace can be shown in a popup.  
  
Plus, Hana is beautiful (also a search on google images proves this statement true... :D ), whereas macros are just ugly, and unnecessary in this specific case, imho.  
  
The last two points are shown in the following screencast, where I first attempt jumping from macro to macro, which obviously doesn't work (resulting in jumping from the first jump-able thing down the file), and then edit the file to show the jumping and popup.  
  
[![asciicast](https://asciinema.org/a/ZulWI2Ez29GhuvQbh9cZo7FLL.svg)](https://asciinema.org/a/ZulWI2Ez29GhuvQbh9cZo7FLL)

---

## Comment 9

> Username: ldionne  
> Created at: 2021-02-10 15:04:43 UTC  
> Url: https://github.com/boostorg/hana/issues/288#issuecomment-776769651  

As Jason said elsewhere, this was initially for forward compatibility with inline namespaces. This hasn't been relevant so far, and I don't think it will, so I'll make the change.
