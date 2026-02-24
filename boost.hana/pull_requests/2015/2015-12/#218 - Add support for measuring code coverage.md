# #218 [CMake] Add support for measuring code coverage. [Closed]

> Username: ldionne  
> Created at: 2015-12-07 13:56:31 UTC  
> Updated at: 2015-12-07 13:59:22 UTC  
> Closed at: 2015-12-07 13:58:59 UTC  
> Url: https://github.com/boostorg/hana/pull/218  

This PR is just for exposition. After experimenting around, it turns out that code coverage results are not very useful because Hana is so full of templates. Basically, we trivially end up with nearly 100% coverage, because all the functions that are instantiated are indeed being run. Instead, something more useful would be a tool to check for uninstantiated templates.

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-12-07 13:58:59 UTC  
> Url: https://github.com/boostorg/hana/pull/218#issuecomment-162530706  

The reason why I won't merge this is because it increases the maintenance burden without actually providing something useful. Instead, I'd rather just document the attempt and close this without merging.

---
