# #484 Resolve Issue #483 [Open]

> Username: AlrondPrime  
> Created at: 2025-04-28 17:47:24 UTC  
> Updated at: 2026-02-11 02:50:29 UTC  
> Url: https://github.com/boostorg/process/pull/484  

Add support of std::initializer_list<string_view> for windows::default_launcher::operator()

---

## Comment 1

> Username: AlrondPrime  
> Created_at: 2025-04-28 20:26:20 UTC  
> Url: https://github.com/boostorg/process/pull/484#issuecomment-2836459671  

The pipeline failed due to changes made around `April 14` in the file `include\boost\process\v2\stdio.hpp` that are incompatible with Windows. Should I fix the errors in this file, or would it be better to fork from another commit and attempt to merge the code there?

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2025-04-29 14:13:59 UTC  
> Url: https://github.com/boostorg/process/pull/484#issuecomment-2839095664  

You can add it to this PR if you want

---

## Comment 3

> Username: AlrondPrime  
> Created_at: 2025-04-29 19:33:53 UTC  
> Url: https://github.com/boostorg/process/pull/484#issuecomment-2840029704  

I’d prefer to focus on the changes I’ve already made, which are unrelated to the issue in `stdio.hpp`.   
I can help fix the Windows compatibility separately later if needed.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2025-04-30 14:48:33 UTC  
> Url: https://github.com/boostorg/process/pull/484#issuecomment-2842245809  

Ok, I'll see that I find the time this week to properly review this PR.

---

## Comment 5

> Username: klemens-morgenstern  
> Created_at: 2025-10-06 04:52:53 UTC  
> Url: https://github.com/boostorg/process/pull/484#issuecomment-3369920927  

This fell of my radar - why do you need an initalizer_list overload for the launcher?

---

## Comment 6

> Username: AlrondPrime  
> Created_at: 2025-10-06 06:12:11 UTC  
> Url: https://github.com/boostorg/process/pull/484#issuecomment-3370053198  

These changes unify the process-launching interface across different launch mechanisms, resulting in a more natural, idiomatic design that simplifies switching between them. You can see an example in the attached issue [483](https://github.com/boostorg/process/issues/483)

---
