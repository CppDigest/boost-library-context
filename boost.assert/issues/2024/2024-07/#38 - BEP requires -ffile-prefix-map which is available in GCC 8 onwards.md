# #38 - BEP requires -ffile-prefix-map which is available in GCC 8 onwards [Closed]

> Username: feng-jianwei  
> Created at: 2024-07-01 06:48:30 UTC  
> Updated at: 2024-07-03 18:47:16 UTC  
> Closed at: 2024-07-03 18:47:16 UTC  
> Url: https://github.com/boostorg/assert/issues/38  

https://github.com/boostorg/assert/blob/425b5ba31b0667439fb72dbf9ce425af6ef1b094/include/boost/assert/source_location.hpp#L180  
Although __builtin_FILE () is  available in gcc7, -ffile-prefix-map is not supported in gcc7. When the gcc7 version is used in a project, there will be bep consistency problems. It is recommended to increase the version number in the macro.  
patch：  
-#elif defined(BOOST_GCC) && BOOST_GCC >= 70000  
+#elif defined(BOOST_GCC) && BOOST_GCC >= 80000  
   
-// The built-ins are available in 4.8+, but are not constant expressions until 7  
+// The built-ins are available in 4.8+, but BEP requires -ffile-prefix-map which is available in GCC 8 onwards

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-01 09:37:04 UTC  
> Url: https://github.com/boostorg/assert/issues/38#issuecomment-2199686640  

I don't understand how this helps. If `__builtin_FILE()` is not used, `__FILE__` will be used instead, and it won't be affected by the nonexistent -ffile-prefix-path either.

---

## Comment 2

> Username: feng-jianwei  
> Created at: 2024-07-02 02:11:15 UTC  
> Updated at: 2024-07-02 02:12:25 UTC  
> Url: https://github.com/boostorg/assert/issues/38#issuecomment-2201679138  

> I don't understand how this helps. If `__builtin_FILE()` is not used, `__FILE__` will be used instead, and it won't be affected by the nonexistent -ffile-prefix-path either.  
  
Although `__FILE__ `can also cause binary differences, the differences caused by `__FILE__ `can be resolved. The difference is that `__builtin_FILE()`is a built-in function, whereas `__FILE__` is a macro definition. We can resolve it by using -D__FILE__.  
![image](https://github.com/boostorg/assert/assets/49725532/853088a4-cd87-4b4c-b732-e511591b6d3d)

---

## Comment 3

> Username: pdimov  
> Created at: 2024-07-02 05:31:45 UTC  
> Url: https://github.com/boostorg/assert/issues/38#issuecomment-2201969936  

What does the BEP abbreviation stand for?

---

## Comment 4

> Username: feng-jianwei  
> Created at: 2024-07-02 06:16:02 UTC  
> Url: https://github.com/boostorg/assert/issues/38#issuecomment-2202026877  

> What does the BEP abbreviation stand for?  
  
BEP=Binary Equivalence Project  
Based on the same source code and the same environment, the same version can be reproducibly built, and the build results are completely consistent.  
it aims to ensure that the software's build process from source code to binary code is consistent and verifiable.  
You can find the Build Path tab in the https://reproducible-builds.org/docs/env-variations/ to learn more about this.
