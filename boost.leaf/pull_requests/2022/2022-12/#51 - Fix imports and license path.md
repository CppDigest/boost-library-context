# #51 [conan] Fix imports and license path [Merged]

> Username: kammce  
> Created at: 2022-12-01 20:45:24 UTC  
> Updated at: 2022-12-02 01:32:04 UTC  
> Merged at: 2022-12-02 00:18:29 UTC  
> Closed at: 2022-12-02 00:18:29 UTC  
> Url: https://github.com/boostorg/leaf/pull/51  

- Bump version to 1.81.0  
- Import fix is simply a cleanup step that has no impact on the conanfile as the import was not used  
- License path was incorrect meaning that the package was previously not included the local conan package cache.

---
