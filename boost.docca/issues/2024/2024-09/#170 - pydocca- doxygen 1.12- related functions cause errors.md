# #170 - pydocca: doxygen 1.12: related functions cause errors [Closed]

> Username: anarthal  
> Created at: 2024-09-02 09:09:02 UTC  
> Updated at: 2024-10-31 11:25:51 UTC  
> Closed at: 2024-10-31 11:25:51 UTC  
> Url: https://github.com/boostorg/docca/issues/170  

Doxygen 1.12 seems to have changed behavior with related functions (using `\relates`). Previously, the function was listed as a member of the class it relates to, only. It is now listed both as a member of the class and of the namespace it's defined in, with the same ID. This yields the same problem as #159.  
  
pydocca seems to assume that members with a certain ID only appear ever once, and that's not the case anymore. It looks like we should be de-duplicating entities somehow (probably checking the index before creating the object), but that's not trivial. I've workarounded it by inserting the member twice with a fake ID, but that probably causes linking issues in some cases.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-09-02 14:08:08 UTC  
> Url: https://github.com/boostorg/docca/issues/170#issuecomment-2324842727  

Side note: according to Sam, the machine building the nightly docs runs [this job](https://github.com/boostorg/boost/blob/master/.circleci/config.yml), which seems to use Doxygen 1.9.5 by default.

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-09-02 14:28:18 UTC  
> Url: https://github.com/boostorg/docca/issues/170#issuecomment-2324880987  

Yeah, we probably shouldn't rely on latest Doxygen features. But at the same time, Docca should work with the latest Doxygen.
