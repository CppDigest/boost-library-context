# #665 X3: Optional parser is not a passthrough parser [Merged]

> Username: Kojoley  
> Created at: 2021-04-19 13:55:30 UTC  
> Updated at: 2023-10-11 01:46:02 UTC  
> Merged at: 2021-04-24 00:45:10 UTC  
> Closed at: 2021-04-24 00:45:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/665  

Removes flat sequence parser through optional to fix:  
* Non-flat `tuple<..., optional<tuple<...>>, ...>` parsing.  
* Optional container parsing when it is a part of the sequence and the optional parser subject is a sequence too.  
  
The removed test cases are invalid (see boostorg/spirit#837) variants of flat through optional. There are a valid ones (like `int_ >> -(int_ >> int_)` into `tuple<int, optional<int>, optional<int>>`) that would be broken after the change, but at the moment I do not have a popper way to make it work.  
  
Fixes boostorg/spirit#519

---

## Comment 1

> Username: Kojoley  
> Created_at: 2021-04-24 00:00:22 UTC  
> Url: https://github.com/boostorg/spirit/pull/665#issuecomment-825998047  

@djowel could I have your opinion on this? I probably will not be able to implement `int_ >> -(int_ >> int_)` into `tuple<int, optional<int>, optional<int>>` support in a correct way anytime soon, and potential codebrake makes me sad, even considering it is undocumented feature.

---

## Comment 2

> Username: djowel  
> Created_at: 2021-04-24 00:22:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/665#issuecomment-826002899  

> @djowel could I have your opinion on this? I probably will not be able to implement `int_ >> -(int_ >> int_)` into `tuple<int, optional<int>, optional<int>>` support in a correct way anytime soon, and potential codebrake makes me sad, even considering it is undocumented feature.  
  
I was about to... Yeah, I was also wondering about the impact. But I'm thinking the bug outweighs the code break? If you agree, then go ahead.

---

## Comment 3

> Username: sehe  
> Created_at: 2023-10-11 00:22:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/665#issuecomment-1756497822  

Hey just a head's up: the subtle change in behaviour actually surfaced a bug in some code :) See https://stackoverflow.com/questions/69896335/how-do-i-make-this-https-connection-persistent-in-beast/69908177?noredirect=1#comment136221455_69908177  
  
Mind you - it's "behaviour change", not a "breaking change" because I think the code was relying non-atomic container attribute side-effects after backtracking. Consider it a funny anecdote, illustrating a variation on Hyrum's Law.

---

## Comment 4

> Username: djowel  
> Created_at: 2023-10-11 01:28:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/665#issuecomment-1756595736  

> Hey just a head's up: the subtle change in behaviour actually surfaced a bug in some code :) See https://stackoverflow.com/questions/69896335/how-do-i-make-this-https-connection-persistent-in-beast/69908177?noredirect=1#comment136221455_69908177  
>   
> Mind you - it's "behaviour change", not a "breaking change" because I think the code was relying non-atomic container attribute side-effects after backtracking. Consider it a funny anecdote, illustrating a variation on Hyrum's Law.  
  
Is this something we need to undo? What is your opinion? I am on the fence.

---

## Comment 5

> Username: sehe  
> Created_at: 2023-10-11 01:45:03 UTC  
> Updated_at: 2023-10-11 01:46:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/665#issuecomment-1756605855  

> Is this something we need to undo? What is your opinion? I am on the fence.  
  
Not on my behalf!   
  
From my superficial glance at the issue boostorg/spirit#519 gather that the fix was legitimate? I just thought it was a fun story to share about how people will - inadvertently - depend on every observable effect of code.  
  
In possibly related news,   
  
 1. I do tend to run into quite a lot of issues with attribute compatibility - especially getting sequences of `vector<char>` attributes to propagate into `std::string` in X3 that never used to be a problem in Qi, and   
 2. I recently ran into breakage that might also have been due to this particular change: https://stackoverflow.com/questions/45673378/boost-spirit-x3-parse-into-structs/45683933#comment135826578_45683933 - I much preferred the old style (in the answer) where rules would bind to the same attribute, instead of needing to resort to semantic actions (in the comment)

---
