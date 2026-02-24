# #58 Bring styles over from boostbook; makes styling consistent across doc types [Merged]

> Username: cdw9  
> Created at: 2024-12-07 01:54:25 UTC  
> Updated at: 2024-12-19 14:14:39 UTC  
> Merged at: 2024-12-19 14:14:28 UTC  
> Closed at: 2024-12-19 14:14:28 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58  

https://github.com/boostorg/website-v2/issues/1484  
  
These changes will allow the docs to maintain their styling after boostbookv2.css is removed

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-12-07 02:02:18 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#issuecomment-2524801508  

An automated preview of the documentation is available at [https://58.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://58.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 2

> Username: cdw9  
> Created_at: 2024-12-09 16:20:56 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#issuecomment-2528550003  

I see a couple things that needs to be adjusted in the preview, so there will be some more changes coming

---

## Comment 3

> Username: julioest  
> Created_at: 2024-12-10 13:52:30 UTC  
> Updated_at: 2024-12-10 14:09:35 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#issuecomment-2531700176  

1. Let's add this rule to remove margin bottom for nested dl tags:  
  
<img width="40%" alt="image" src="https://github.com/user-attachments/assets/91490093-d6df-4951-ae79-b3659a5f4baa">  
  
```css  
.boostlook dl.toc dd dl dl {  
  margin-bottom: 0;  
}  
```   
  
  
2. Also, let's use the direct child combinator to keep extra padding in check for <dd> elements:  
  
```css  
.boostlook dl.toc > dd {...}  
```  
3. The home icon in boostbook libs should match antora's

---

## Review 4 [Changes requested]

> Username: julioest  
> Created_at: 2024-12-10 14:13:22 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boostlook/pull/58#pullrequestreview-2492250411  

Overall, it looks great! Added some comments for this commit and also others I missed previously.

📁 boostlook.css

```diff
 418 |+ .boostlook ul.itemizedlist,
 419 |+ #boost-legacy-docs-wrapper ul {
 420 |+   padding: 0 0 0 15px;
```

> Username: julioest  
> Created_at: 2024-12-10 12:58:04 UTC  
> Updated_at: 2024-12-10 14:13:22 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#discussion_r1878050528  

Just need a little more specificity here to avoid changes in Antora  
  
<img width="1512" alt="image" src="https://github.com/user-attachments/assets/1ed03301-c08c-466a-a9de-024953302a7c">

> Username: cdw9  
> Created_at: 2024-12-17 23:06:32 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#discussion_r1889353750  

this should be fixed in the latest commit

---

📁 boostlook.css

```diff
1241 |+   border-radius: 8px;
1242 |+   padding: 1.2em .8em
1243 |+ }
```

> Username: julioest  
> Created_at: 2024-12-10 14:12:06 UTC  
> Updated_at: 2024-12-10 14:13:22 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#discussion_r1878172511  

Let's add a margin declaration to address horizontal spacing for pre tags  
  
<img width="1512" alt="image" src="https://github.com/user-attachments/assets/a6fea309-6b93-4d42-9d22-dcb28f431cb2">

> Username: cdw9  
> Created_at: 2024-12-17 23:06:20 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#discussion_r1889353589  

this should be fixed in the latest commit


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-12-13 15:27:24 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#issuecomment-2541699529  

An automated preview of the documentation is available at [https://58.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://58.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-12-17 23:11:19 UTC  
> Url: https://github.com/boostorg/boostlook/pull/58#issuecomment-2549850634  

An automated preview of the documentation is available at [https://58.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://58.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Review 7 [Approved]

> Username: rbbeeston  
> Created_at: 2024-12-17 23:25:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/58#pullrequestreview-2510392087  

LGTM

---
