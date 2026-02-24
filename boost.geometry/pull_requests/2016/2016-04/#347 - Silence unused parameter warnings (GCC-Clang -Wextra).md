# #347 Silence unused parameter warnings (GCC/Clang -Wextra). [Merged]

> Username: plopresti  
> Created at: 2016-04-13 22:08:01 UTC  
> Updated at: 2016-05-28 21:47:38 UTC  
> Merged at: 2016-05-28 21:47:38 UTC  
> Closed at: 2016-05-28 21:47:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/347  

For all I know, some of these might even be real bugs...  
  
But in any event, it's nice when Boost does not generate warnings for those of us who like to enable lots and lots of warnings.

---

## Comment 1

> Username: mloskot  
> Created_at: 2016-04-14 09:05:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-209838246  

Why not `boost::ignore_unused_variable_warning` which is already used across the codebase?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2016-04-14 11:35:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-209890518  

There is shorter version: `boost::ignore_unused()`.  
But I also prefer removing or commenting out unused parameters, I think workarounds should be used only if there is no other way.

---

## Comment 3

> Username: mloskot  
> Created_at: 2016-04-14 11:58:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-209903009  

@awulkiew Sure, whatever is the current and preferred convention. I vaguely recall the explicit `ignore_used` was preferred as it also was an explicit comment visible in the code.

---

## Comment 4

> Username: plopresti  
> Created_at: 2016-04-14 14:24:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-209968351  

Removing or commenting out a parameter name is 100% standard C++, it's the standard way to silence this warning in any code base, and it can only mean one thing (so it functions perfectly as "an explicit comment visible in the code").  
  
Why would anyone prefer something both more verbose and specific to Boost?  
  
Thanks, Adam.

---

## Comment 5

> Username: mloskot  
> Created_at: 2016-04-14 14:58:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-209986511  

@plopresti I'm not saying it is not standard way, but sometimes project-specific guidelines recommend different ways. For the same reason some accept `T* p=0`, other projects will require `T* p=nullptr`. I'm not arguing with you here, I've just (mis)recalled some very old discussions.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2016-04-14 16:13:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-210027599  

Another way would be removing some of these parameters entirely but AFAIU it cannot be done.  
Barend what do you think?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2016-05-28 12:09:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-222305389  

Barend, I'd like to hear your opinion before merging this PR.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2016-05-28 12:49:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/347#issuecomment-222306882  

I'm OK with merging - the parameters can also be removed, but commenting is also fine for me.

---
