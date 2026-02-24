# #67 Add crappy starter bash scripts for awesome visualization build [Open]

> Username: evanlenz  
> Created at: 2021-04-01 09:16:00 UTC  
> Updated at: 2021-07-19 18:40:21 UTC  
> Url: https://github.com/boostorg/docca/pull/67  

relates to #66

---

## Comment 1

> Username: grisumbras  
> Created_at: 2021-04-06 08:23:36 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-813931128  

Ok, so my starting idea is to use some standard b2 feature ("feature" in b2 parlance is property without value) to select modes.  
Either `variant` or `debug-symbols`.  
So `b2 doc variant=debug` or `b2 doc debug-symbols=on` would result in visualized build.  
  
Mode selection would be implemented using target alternatives:  
```  
make stage1.xsl : $(src-dir)/stage1.xsl : @common.copy ;  # this is the default alternative  
make stage1.xsl : $(src-dir)/stage1.xsl  $(src-dir)/trace-enable.xsl : @saxonhe.saxonhe : <debug-symbols>on ;  # this is visualised alternative  
```

---

## Comment 2

> Username: evanlenz  
> Created_at: 2021-04-09 05:06:16 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-816407241  

This makes sense to me. I personally would lean toward using the `variant` feature, since "debug symbols" seems to imply something that is specific to object files or executables of a compiled language, unlike XSLT. Either seems fine though.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-04-09 17:14:43 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-816829630  

MFW you two have become better at bjam than me

---

## Comment 4

> Username: evanlenz  
> Created_at: 2021-05-21 06:34:35 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-845695273  

@grisumbras Just checking in to see if I can help with this at all or if you have any other questions.

---

## Comment 5

> Username: grisumbras  
> Created_at: 2021-05-28 13:16:15 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-850411735  

I sadly won't have time to dedicate to this task until July. I have a basic idea of what needs to be done, and if I need any help I'll ping you.

---

## Comment 6

> Username: evanlenz  
> Created_at: 2021-06-01 17:46:09 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-852324351  

> I sadly won't have time to dedicate to this task until July. I have a basic idea of what needs to be done, and if I need any help I'll ping you.  
  
No worries. That sounds great, thanks!

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-06-21 17:30:56 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-865217158  

What's going on with this?

---

## Comment 8

> Username: grisumbras  
> Created_at: 2021-06-21 17:33:33 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-865218622  

Nothing right now. But I plan to work on b2 integration in July.

---

## Comment 9

> Username: evanlenz  
> Created_at: 2021-07-15 07:18:36 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-880458682  

@grisumbras I'll be out for the next couple days, but if you think an orientation meeting on this would be useful, let me know and we'll set something up next week! I'm looking forward to seeing what you come up with, given how impressed I was with the docca jam module you put together.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2021-07-19 17:51:15 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-882741100  

What's the status on this?

---

## Comment 11

> Username: evanlenz  
> Created_at: 2021-07-19 18:40:21 UTC  
> Url: https://github.com/boostorg/docca/pull/67#issuecomment-882772252  

@grisumbras @vinniefalco Everything Dmitry needs to work from is theoretically included in this pull request, but I could provide some orientation around it and maybe a demo if he thinks it would be helpful.

---
