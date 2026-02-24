# #123 - "Edit this page" link [Open]

> Username: vinniefalco  
> Created at: 2022-04-06 16:35:46 UTC  
> Updated at: 2022-04-06 17:59:18 UTC  
> Url: https://github.com/boostorg/docca/issues/123  

Would it be possible to have an "edit this page" link on every generated doc page and in each page of the exposition? I guess it should link to the GitHub repository, I believe GitHub has some API that lets you edit a file in the repo.

---

## Comment 1

> Username: evanlenz  
> Created at: 2022-04-06 17:54:00 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090563765  

Ooh, that sounds cool (and pretty feasible, I think). However, the trick would be preserving the changes after another doc build. Without some relatively fancy mechanism (and I have no aversion to relatively fancy mechanisms when they do something valuable!), the manual changes would get clobbered in the very next build (and would have to manually get reapplied). Does this all make sense what I’m saying? Or did I misunderstand what you’re asking about?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-04-06 17:55:08 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090564821  

uhhhhh...GitHub has an inline editor which submits the change as a pull request

---

## Comment 3

> Username: evanlenz  
> Created at: 2022-04-06 17:55:57 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090565601  

Oh, I think I did misunderstand! You’re talking about editing the source files! Duh, brain is a little slow today. 😅 Yes, we should be able to make this work. Devil in the details, but any obstacles have got to be surmountable.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-04-06 17:56:32 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090566139  

![image](https://user-images.githubusercontent.com/1503976/162038116-2f03a4cc-3f1b-40e8-8c86-a62ca5e6f336.png)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-04-06 17:57:10 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090566637  

The link is formed like this  
```  
https://github.com/boostorg/docca/edit/develop/LICENSE_1_0.txt  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-04-06 17:58:14 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090567552  

it could only apply to the develop branch though.... so if you are trying to edit say, an older Boost version (1.77 for example) when you press the button you will be editing the develop branch version of the file.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-04-06 17:58:33 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090567845  

This is starting to sound lame... lol

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-04-06 17:59:18 UTC  
> Url: https://github.com/boostorg/docca/issues/123#issuecomment-1090568539  

There's no way to bring the edit box focus directly to the line in question, and there's no way for the xslt to know which line in the source file it corresponds to in the first place. Not very streamlined.
