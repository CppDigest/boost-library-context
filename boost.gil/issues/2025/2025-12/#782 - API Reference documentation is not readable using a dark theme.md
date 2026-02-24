# #782 - API Reference documentation is not readable using a dark theme [Open]

> Username: planetmarshall  
> Created at: 2025-12-08 18:32:45 UTC  
> Updated at: 2025-12-09 20:11:20 UTC  
> Url: https://github.com/boostorg/gil/issues/782  

### Actual behavior  
  
API Documentation is not readable under a "Dark Mode" system theme  
  
### Expected  behavior  
  
API Documentation should be readable regardless of the system theme applied by the user  
  
### Steps to reproduce  
  
1. In Firefox (or similar) Apply Settings->Language and Appearance->Dark  
    Alternatively, select "Automatic" and apply a Dark system theme (eg in Gnome, Settings->Dark Style)  
2. Navigate to https://www.boost.org/doc/libs/latest/libs/gil/doc/html/reference/group___image_processing.html  
  
<img width="1112" height="1000" alt="Image" src="https://github.com/user-attachments/assets/ae566d9a-1650-4f3e-a859-0cbeb7f7635a" />

---

## Comment 1

> Username: sdebionne  
> Created at: 2025-12-09 14:24:19 UTC  
> Url: https://github.com/boostorg/gil/issues/782#issuecomment-3632526629  

I have the same issue with Chrome. Let's try to improve that for the next release. I wonder if moving to   
- Asciidoc to be in the same doc framework than newer Boost libraries  
- `sphinx-immaterial` theme to get a more modern UX

---

## Comment 2

> Username: mloskot  
> Created at: 2025-12-09 19:58:58 UTC  
> Updated at: 2025-12-09 20:06:40 UTC  
> Url: https://github.com/boostorg/gil/issues/782#issuecomment-3634028188  

I've just opened  
- https://github.com/boostorg/gil/issues/783  
  
and I should be able to look into the migrating it over the xmas break. Does it sound like a good plan?

---

## Comment 3

> Username: sdebionne  
> Created at: 2025-12-09 20:11:19 UTC  
> Url: https://github.com/boostorg/gil/issues/782#issuecomment-3634070586  

Awesome, thanks ! 👍
