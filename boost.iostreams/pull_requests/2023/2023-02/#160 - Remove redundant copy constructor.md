# #160 Remove redundant copy constructor. [Open]

> Username: ecatmur  
> Created at: 2023-02-22 20:01:58 UTC  
> Updated at: 2023-02-22 20:21:04 UTC  
> Url: https://github.com/boostorg/iostreams/pull/160  

Since C++11, a user-declared copy constructor deprecates the copy assignment operator.  
  
This change allows clean compilation with the -Wdeprecated-copy/-Wdeprecated-copy-with-user-provided-ctor flag.

---

## Comment 1

> Username: mclow  
> Created_at: 2023-02-22 20:09:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/160#issuecomment-1440734701  

is this redundant? In C++03?  
An alternative approach would be to define the copy-assignment operator.

---

## Comment 2

> Username: ecatmur  
> Created_at: 2023-02-22 20:21:04 UTC  
> Url: https://github.com/boostorg/iostreams/pull/160#issuecomment-1440747058  

Yes, this is Note 113 in C++03:  
  
> 113) Because a template constructor is never a copy constructor, the presence of such a template does not suppress the implicit declaration of a copy  
constructor. Template constructors participate in overload resolution with other constructors, including copy constructors, and a template constructor  
may be used to copy an object if it provides a better match than other constructors.  
  
I can add the copy assignment operator if you'd prefer.

---
