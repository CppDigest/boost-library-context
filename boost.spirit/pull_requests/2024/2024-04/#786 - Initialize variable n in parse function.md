# #786 Initialize variable n in parse function [Closed]

> Username: vissarion  
> Created at: 2024-04-25 08:51:59 UTC  
> Updated at: 2024-04-25 09:52:10 UTC  
> Closed at: 2024-04-25 09:52:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/786  

This PR intents to prevent a maybe uninitialized warning that appear with recent versions of gcc (11 and 12).

---
