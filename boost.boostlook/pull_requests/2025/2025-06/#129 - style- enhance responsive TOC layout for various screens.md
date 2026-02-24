# #129 style: enhance responsive TOC layout for various screens [Merged]

> Username: julioest  
> Created at: 2025-06-16 18:37:44 UTC  
> Updated at: 2025-06-17 13:34:57 UTC  
> Merged at: 2025-06-17 13:34:57 UTC  
> Closed at: 2025-06-17 13:34:57 UTC  
> Url: https://github.com/boostorg/boostlook/pull/129  

### **Description**  
Improves AsciiDoctor TOC layout responsiveness across screen sizes to address wide screen display issues.  
  
Reference: https://github.com/boostorg/website-v2/issues/1806  
  
  
### **Key changes**  
- Simplified article layout to use consistent offset behavior instead of centering  
- Fixed TOC positioning at tablet/desktop breakpoints (768px+) with left-aligned sidebar  
- Added progressive content width scaling for large (1280px+), wide (1536px+), and ultra-wide (1920px+) screens  
- Removed centering calculations that caused layout jumps, when resizing viewport  
- Enhanced content spacing and margins for better readability on wide displays  
- Resolves TOC positioning inconsistencies and improves content modern wide screens  
  
### Screenshots  
**1440px**  
![1440px](https://github.com/user-attachments/assets/18a7680b-194d-4c6e-925c-9c7c7dde11ca)  
**1536px**  
![1536px](https://github.com/user-attachments/assets/080b5473-3013-45dd-baca-d6dfa282687f)  
**1920px**  
![1920px](https://github.com/user-attachments/assets/145d5cef-f5a7-4d38-8f96-044b07802bb5)

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-06-16 18:47:42 UTC  
> Url: https://github.com/boostorg/boostlook/pull/129#issuecomment-2977707211  

An automated preview of the documentation is available at [https://129.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html](https://129.boostlook.prtest2.cppalliance.org/tools/boostlook/doc/html/index.html)

---

## Comment 2

> Username: julioest  
> Created_at: 2025-06-16 19:01:53 UTC  
> Url: https://github.com/boostorg/boostlook/pull/129#issuecomment-2977751701  

Hey there, @mborland. How's this look on your screen?

---

## Comment 3

> Username: mborland  
> Created_at: 2025-06-16 19:03:51 UTC  
> Updated_at: 2025-06-16 19:05:08 UTC  
> Url: https://github.com/boostorg/boostlook/pull/129#issuecomment-2977757631  

Looks like serious improvement to me:  
  
<img width="1826" alt="image" src="https://github.com/user-attachments/assets/8fc4d628-f362-4bd4-b145-cd7018f170a0" />  
  
Edit: It's Safari on macOS15 and screen resolution of 2560x1440

---

## Review 4 [Approved]

> Username: rbbeeston  
> Created_at: 2025-06-16 20:30:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/129#pullrequestreview-2933425420  

LGTM, just have Kari sign off

---

## Review 5 [Approved]

> Username: karimarie67  
> Created_at: 2025-06-17 12:50:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boostlook/pull/129#pullrequestreview-2935538644  

Tested in 1440, 1536 and 1920 - all looked good. Ready to go!

---
