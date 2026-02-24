# #49 - move implicit_cast to core? [Closed]

> Username: HDembinski  
> Created at: 2019-04-18 12:55:56 UTC  
> Updated at: 2020-01-20 17:03:18 UTC  
> Closed at: 2020-01-20 17:03:18 UTC  
> Url: https://github.com/boostorg/core/issues/49  

The boost.conversion library contains `implicit_cast`, which has a simple implementation, no external dependencies and seems useful to have in boost.core. It would also be nice to document the cast better, for me https://stackoverflow.com/questions/868306/what-is-the-difference-between-static-cast-and-implicit-cast was useful to read.

---

## Comment 1

> Username: anshu-khare-design  
> Created at: 2020-01-16 15:43:24 UTC  
> Url: https://github.com/boostorg/core/issues/49#issuecomment-575211453  

I have worked on this.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-01-20 17:03:18 UTC  
> Url: https://github.com/boostorg/core/issues/49#issuecomment-576360872  

@HDembinski I don't think we want to do this. Unlike say, `use_default`, this doesn't exist on public interfaces and is trivial enough to live in detail namespaces if a dependency on Conversion is not desirable. No dependency cycles are broken or would be avoided by relocating this facility.
