# #224 - alnum/alpha parser [Closed]

> Username: psiha  
> Created at: 2025-04-22 08:45:30 UTC  
> Updated at: 2025-07-12 21:10:56 UTC  
> Closed at: 2025-07-12 21:10:56 UTC  
> Url: https://github.com/boostorg/parser/issues/224  

Could you please add those or at least document that those actually are not present and why?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:22:24 UTC  
> Url: https://github.com/boostorg/parser/issues/224#issuecomment-2832662127  

You're asking me to detail why two things that don't exist ... don't exist.  Please define what you mean.  If you for instance mean parsers that recognize what std::alnum and std::alpha recognize, those functions are busted and no one should use them under any circumstances.  They are not even correct for ASCII.  If you meant something else, what did you mean?

---

## Comment 2

> Username: psiha  
> Created at: 2025-05-05 12:03:52 UTC  
> Url: https://github.com/boostorg/parser/issues/224#issuecomment-2850778072  

Well [X3](https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/quick_reference/char.html) provides those and you document other areas of (dis)similarities with it so this also seemed like an 'obvious' omission (even if just saying 'alnum is broken beyond repair, won't do it, make your own').

---

## Comment 3

> Username: saki7  
> Created at: 2025-05-10 04:20:23 UTC  
> Url: https://github.com/boostorg/parser/issues/224#issuecomment-2868335126  

Hello, I just noticed this thread while randomly browsing GitHub.  
Boost.Spirit.X3 maintainer here :)  
  
@psiha Please do NOT say statements like "X3 provides this feature but your library does not...".  
You're simply harassing a open source maintainer. I, as a X3 maintainer, do not want to hear such thing from a user.  
  
An open source project does not work like that. Either you submit your own PR or just wait for generous people to implement it. You have no right to say such rude things to the maintainer. That kind of attitude can not be tolerated.

---

## Comment 4

> Username: psiha  
> Created at: 2025-05-10 18:37:10 UTC  
> Url: https://github.com/boostorg/parser/issues/224#issuecomment-2869082198  

> Hello, I just noticed this thread while randomly browsing GitHub. Boost.Spirit.X3 maintainer here :)  
>   
> [@psiha](https://github.com/psiha) Please do NOT say statements like "X3 provides this feature but your library does not...". You're simply harassing a open source maintainer. I, as a X3 maintainer, do not want to hear such thing from a user.  
>   
> An open source project does not work like that. Either you submit your own PR or just wait for generous people to implement it. You have no right to say such rude things to the maintainer. That kind of attitude can not be tolerated.  
  
Hi saki7, I understand where you're coming from but would still consider it a 'bit' of a stretch to call it 'rude harassement' even if I actually did say what you insinuated me as saying. Go read my messages again - they seem clear to me in saying that the library _already_ compares itself to X3 (in some sense it is considered its successor) and I found an area where this comparison is missing something so I reported it...  
Sorry for the confusion, hope this helps clear it up :)

---

## Comment 5

> Username: tzlaine  
> Created at: 2025-07-12 19:50:50 UTC  
> Url: https://github.com/boostorg/parser/issues/224#issuecomment-3066005612  

@saki7, thanks for chiming in, but I wasn't actually offended.  
  
@psiha, I understand what you're saying, and I think the fix is simply to document this in the Rationale section of the docs.  Documenting why stuff is not in the library is literally an infinite task, so I don't think that belongs in the main tutorial text.  That being said, if I get enough questions about this, it will rise to the level of a FAQ, and I can move the explanation to the main tutorial text.
