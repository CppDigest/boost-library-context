# #150 prevent unused warnings in x3 [Closed]

> Username: bebuch  
> Created at: 2015-08-14 19:02:25 UTC  
> Updated at: 2016-05-17 16:20:46 UTC  
> Closed at: 2016-05-12 16:36:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/150  

I'm not quite sure about the macro magic in "include/boost/spirit/home/x3/nonterminal/rule.hpp" :/

---

## Comment 1

> Username: tomilov  
> Created_at: 2015-08-14 19:23:06 UTC  
> Updated_at: 2015-08-14 19:25:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/150#issuecomment-131213992  

Definitely bad idea.

---

## Comment 2

> Username: bebuch  
> Created_at: 2015-08-14 22:03:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/150#issuecomment-131250819  

Do you mean only the macro magic change or the pull request as a whole? I looked at it more closely and think it is okay, but it is the first time I tried to understand boost.preprocessor.  
  
It would be nice of you if you explain why it is a bad idea. :-)

---

## Comment 3

> Username: djowel  
> Created_at: 2015-08-14 23:05:15 UTC  
> Updated_at: 2015-08-14 23:07:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/150#issuecomment-131264707  

For unused arguments, the preferred style is to comment out the argument name instead of deleting. Example:   
  
  bool parse(Iterator& /_first_/, Iterator const& /_last_/,   
    Context const& /_context_/, RuleContext&, Attribute& attr_) const

---

## Comment 4

> Username: bebuch  
> Created_at: 2015-08-15 00:53:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/150#issuecomment-131274742  

Okay, I will do that, thank you!

---

## Comment 5

> Username: bebuch  
> Created_at: 2016-05-12 16:36:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/150#issuecomment-218813786  

Close pull request. I will re-pull with simpler changes.

---
