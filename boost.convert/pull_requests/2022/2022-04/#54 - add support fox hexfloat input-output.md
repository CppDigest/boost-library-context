# #54 add support fox hexfloat input/output [Closed]

> Username: dvirtz  
> Created at: 2022-04-13 20:00:58 UTC  
> Updated at: 2022-04-14 06:48:09 UTC  
> Closed at: 2022-04-14 05:30:38 UTC  
> Url: https://github.com/boostorg/convert/pull/54  

Currently only `printf` and `stream` converters as the other converters don't seem to support configuring notation.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2022-04-13 20:15:47 UTC  
> Url: https://github.com/boostorg/convert/pull/54#issuecomment-1098445779  

Tnx for your interest and contribution. Much appreciated. I'll look into it in a couple of days. Tnx.

---

## Comment 2

> Username: yet-another-user  
> Created_at: 2022-04-14 00:05:36 UTC  
> Url: https://github.com/boostorg/convert/pull/54#issuecomment-1098587151  

I've just added the requested functionality... although implemented it differently from the way you suggested. Apologies. Tests passed. Please see if that's to your satisfaction.

---

## Comment 3

> Username: dvirtz  
> Created_at: 2022-04-14 05:30:38 UTC  
> Url: https://github.com/boostorg/convert/pull/54#issuecomment-1098725032  

I'd rather you request the changes to be made to this PR but the important thing is to have the functionality.  
Is the reason for your implementation to have notation in `cnvbase`?  
  
I'm also planning on adding more backends, e.g. `charconv`. Do you prefer to implement them yourself as well?

---

## Comment 4

> Username: yet-another-user  
> Created_at: 2022-04-14 06:20:41 UTC  
> Updated_at: 2022-04-14 06:21:08 UTC  
> Url: https://github.com/boostorg/convert/pull/54#issuecomment-1098750230  

RE: Is the reason for your implementation to have notation in cnvbase?  
All the configuration parameters are in the base. That way other backends will take advantage and will make use of those configuration parameters as currently there are 3 converters inheriting from the base. That's the idea anyway.

---
