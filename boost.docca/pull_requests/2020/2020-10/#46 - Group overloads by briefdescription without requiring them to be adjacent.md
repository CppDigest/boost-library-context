# #46 Group overloads by briefdescription without requiring them to be adjacent [Closed]

> Username: evanlenz  
> Created at: 2020-10-17 07:45:43 UTC  
> Updated at: 2020-10-19 06:10:34 UTC  
> Closed at: 2020-10-18 15:52:36 UTC  
> Url: https://github.com/boostorg/docca/pull/46  

This fixes #44. (The C++ may also need some javadoc comments added to the two constructors grouped under the currently-blank heading.)

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2020-10-17 18:17:59 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711060276  

One of the briefs is still missing:  
![image](https://user-images.githubusercontent.com/1503976/96350366-6874e080-106a-11eb-96ab-2b4dad278adc.png)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-10-17 18:18:39 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711060353  

The groups are together and share the brief, but there are two light gray bounding boxes. I guess we can live with that:  
![image](https://user-images.githubusercontent.com/1503976/96350413-80e4fb00-106a-11eb-9907-88844c41b7be.png)

---

## Comment 3

> Username: evanlenz  
> Created_at: 2020-10-17 20:01:19 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711072512  

Ah, I missed the second bullet point. I'll address that as well.  
  
As for the space between the bounding boxes, that is due to the empty briefdescription for those two constructors. It works out sort of okay in this situation, but we shouldn't live with it, because it might not be pseudo-grouped appropriately next time.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2020-10-17 20:07:12 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711073218  

I think this whole thing should be squashed down to one commit, and in the commit description you can put a bulleted list of things changed (i.e. the text from the individual commits).  
  
Rationale: If you look at the total changes, it is very little overall change, and it is easier to comprehend the set of changes as one diff instead of three. Up to you though, whatever is easier to maintain.

---

## Comment 5

> Username: evanlenz  
> Created_at: 2020-10-18 07:06:57 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711126990  

I squashed it down to two commits, one for each fix on a different page.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-10-18 15:05:13 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711205241  

Sensible

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2020-10-18 15:07:12 UTC  
> Url: https://github.com/boostorg/docca/pull/46#issuecomment-711206667  

`DISTRIBUTE_GROUP_DOC` needs to be documented, I will add it to README.md

---
