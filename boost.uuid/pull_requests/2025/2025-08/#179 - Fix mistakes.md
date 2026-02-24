# #179 Fix mistakes [Merged]

> Username: ivanpanch  
> Created at: 2025-08-09 19:08:58 UTC  
> Updated at: 2025-09-09 13:52:46 UTC  
> Merged at: 2025-09-09 13:52:45 UTC  
> Closed at: 2025-09-09 13:52:45 UTC  
> Url: https://github.com/boostorg/uuid/pull/179  

I have corrected a few minor mistakes.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-09-09 10:08:09 UTC  
> Url: https://github.com/boostorg/uuid/pull/179#issuecomment-3269931868  

I want some of these changes but not all. E.g. I don't want the semicolon changes, or moving the period outside the parentheses.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-09-09 10:09:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/179#issuecomment-3269938013  

I suppose I can cherry-pick the commits I want.

---

## Comment 3

> Username: ivanpanch  
> Created_at: 2025-09-09 11:54:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/179#issuecomment-3270347375  

@pdimov The reason I changed the semicolons is that the first word of the next item is capitalized (as in a new sentence). If you want to retain the semicolon, consider not capitalizing. Where I moved a period outside parentheses, it is because the sentence was not enclosed in parentheses as a whole.

---

## Comment 4

> Username: ivanpanch  
> Created_at: 2025-09-09 11:59:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/179#issuecomment-3270370253  

For instance, these variants would be correct:  
  
> This is a sentence (see p. 23 for more information).  
> This is a sentence. (See p. 23 for more information.)  
  
On the other hand, this is something that I reject:  
  
> This is a sentence (see p. 23.)

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-09-09 13:52:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/179#issuecomment-3270850272  

OK, let's go with that.

---
