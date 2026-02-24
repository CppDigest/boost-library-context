# #690 - Sanitizers on MacOS [Closed]

> Username: alandefreitas  
> Created at: 2023-02-21 17:38:10 UTC  
> Updated at: 2023-02-22 19:24:05 UTC  
> Closed at: 2023-02-22 19:24:05 UTC  
> Url: https://github.com/boostorg/url/issues/690  

Boostorg.core has it:  
  
https://github.com/boostorg/core/blob/19f9aa93e1a9c76e9e5aac21c1a6f175e1583b1a/.drone.jsonnet#L337-L357  
  
![image](https://user-images.githubusercontent.com/5369819/220418485-f2e9466d-9554-4c1f-9716-95394e7938cc.png)  
  
but it uses a custom `macos_pipeline` instead of `osx_cxx` from `@boost_ci//ci/drone/:functions.star`.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2023-02-21 17:38:56 UTC  
> Url: https://github.com/boostorg/url/issues/690#issuecomment-1438865471  

@sdarwin does it look like we would need to change `osx_cxx` to support macos+asan?

---

## Comment 2

> Username: sdarwin  
> Created at: 2023-02-21 17:54:05 UTC  
> Url: https://github.com/boostorg/url/issues/690#issuecomment-1438884202  

The way to trigger asan is to set environment['B2_ASAN'] = '1' which then has an effect in the boostorg/boost-ci scripts.     
  
The `generate` function adds that environment variable for linux asan. Try the same step in macOS asan, and observe the results.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2023-02-21 18:06:17 UTC  
> Url: https://github.com/boostorg/url/issues/690#issuecomment-1438898660  

This makes everything extremely easier! Thanks!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-02-22 14:59:28 UTC  
> Url: https://github.com/boostorg/url/issues/690#issuecomment-1440202393  

beautiful, and this is something that is set in the framework script, right? so all projects using the new generator will get the settings

---

## Comment 5

> Username: alandefreitas  
> Created at: 2023-02-22 15:24:06 UTC  
> Url: https://github.com/boostorg/url/issues/690#issuecomment-1440249957  

Yes. When `asan=True` we can create a GCC-Asan job with the latest GCC. All I will do is also create another MacOS-Asan job when `asan=True`.
