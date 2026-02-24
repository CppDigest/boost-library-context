# #1104 - Benchmarks page missing images [Closed]

> Username: kisielk  
> Created at: 2025-09-04 19:26:18 UTC  
> Updated at: 2025-09-04 20:27:53 UTC  
> Closed at: 2025-09-04 20:18:43 UTC  
> Url: https://github.com/boostorg/json/issues/1104  

The images on https://www.boost.org/doc/libs/1_89_0/libs/json/doc/html/benchmarks.html do not load, the server returns a 404 status code.  
  
<img width="1670" height="1018" alt="Image" src="https://github.com/user-attachments/assets/9cfe027b-c7a9-4ace-9898-9332e7f262ed" />

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-09-04 19:30:43 UTC  
> Url: https://github.com/boostorg/json/issues/1104#issuecomment-3255298643  

The images were loading not long ago. @sdarwin are they present in the intended directory?

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-09-04 19:35:32 UTC  
> Url: https://github.com/boostorg/json/issues/1104#issuecomment-3255317305  

The images are present on this page: https://www.boost.org/doc/libs/1_88_0/libs/json/doc/html/json/benchmarks.html

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-09-04 19:42:45 UTC  
> Url: https://github.com/boostorg/json/issues/1104#issuecomment-3255340820  

This is my mistake. I somehow forgot to update the image placement for the new documentation structure.

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-09-04 19:46:43 UTC  
> Url: https://github.com/boostorg/json/issues/1104#issuecomment-3255353231  

The images exist in the previous location:    
`libs/json/doc/html/json/images/`  
The documentation structure has moved up a level, so the last `json` directory is mostly unused. However the benchmark images are still in there.

---

## Comment 5

> Username: grisumbras  
> Created at: 2025-09-04 20:21:32 UTC  
> Url: https://github.com/boostorg/json/issues/1104#issuecomment-3255480193  

The images should be soon present on https://www.boost.org/doc/libs/develop/libs/json/doc/html/benchmarks.html. The 1.89.0 page is unfortunately forever broken.

---

## Comment 6

> Username: sdarwin  
> Created at: 2025-09-04 20:27:53 UTC  
> Url: https://github.com/boostorg/json/issues/1104#issuecomment-3255508488  

when it is definitely fixed, I can upload the files to the S3 folder, and solve the issue, unless you believe it contains other changes that don't apply to 1.89.0.
