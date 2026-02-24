# #261 - boostlook.css in docs [Open]

> Username: sdarwin  
> Created at: 2025-03-06 18:38:44 UTC  
> Updated at: 2025-03-06 20:54:21 UTC  
> Url: https://github.com/boostorg/charconv/issues/261  

Are the boostlook.css styles being inlined to the docs/ ?  
  
Instead of that could `b2` copy the entire file boostlook.css, and reference it as a file, such as css/boostlook.css ?  
  
Or, even better,  
  
Just link to the copy here:  
  
../../../../tools/boostlook/boostlook.css  (check the number of ../../ )  
  
This allows the charconv docs to refer to a local copy of the file, and on the website @Rbbeeston and @daveoconnor are considering swapping it out, on the web frontend, to another (newer) boostlook.css , which would then be possible.

---

## Comment 1

> Username: mborland  
> Created at: 2025-03-06 18:54:39 UTC  
> Url: https://github.com/boostorg/charconv/issues/261#issuecomment-2704688059  

The [Jamfile](https://github.com/boostorg/charconv/blob/develop/doc/Jamfile) is basically copy paste from the example of integration [here](https://github.com/boostorg/boostlook?tab=readme-ov-file#boostlook). Is it as simple as changing the path constant to: `../../../../tools/boostlook/boostlook.css`?

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-03-06 20:09:08 UTC  
> Url: https://github.com/boostorg/charconv/issues/261#issuecomment-2704836038  

That's a good question. Other libraries are able to link in css stylesheets from the superproject, such as  
  
```  
<link rel="stylesheet" href="../../../../doc/src/boostbook.css" type="text/css">  
```

---

## Comment 3

> Username: mborland  
> Created at: 2025-03-06 20:33:23 UTC  
> Url: https://github.com/boostorg/charconv/issues/261#issuecomment-2704879840  

Do you know which other libraries do this? I can try and copy and paste from them

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-03-06 20:36:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/261#issuecomment-2704885870  

The above boostbook.css link was from boostorg/beast.       
Many libraries use boostbook.

---

## Comment 5

> Username: rbbeeston  
> Created at: 2025-03-06 20:54:20 UTC  
> Url: https://github.com/boostorg/charconv/issues/261#issuecomment-2704917793  

Keep in mind, boostbook and boostlook are two different css libs for the styling.
