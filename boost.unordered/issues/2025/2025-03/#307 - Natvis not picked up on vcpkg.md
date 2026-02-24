# #307 - Natvis not picked up on vcpkg [Closed]

> Username: enginelesscc  
> Created at: 2025-03-15 17:33:22 UTC  
> Updated at: 2026-01-01 20:00:50 UTC  
> Closed at: 2026-01-01 20:00:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/307  

The current cmake script seems to be failing to pass the natvis to VS when consumed by vcpkg.  
I've had to manually copy the natvis file to my source directory to get it loaded.  
  
I'm guessing this is due to unordered being an interface in cmake while also marking the natvis file private, so there is neither a lib to consume, nor a config to pass down to consuming projects.  
  
Unfortunately, without that natvis file, unordered_* is not a pleasant experience while debugging :/  
  
(Version: 1.87.0, did not work on 1.86.0 either)

---

## Comment 1

> Username: pdimov  
> Created at: 2025-03-15 18:46:58 UTC  
> Url: https://github.com/boostorg/unordered/issues/307#issuecomment-2726929556  

Do you link to Boost::unordered in your CMakeLists.txt?

---

## Comment 2

> Username: enginelesscc  
> Created at: 2025-03-15 19:03:47 UTC  
> Url: https://github.com/boostorg/unordered/issues/307#issuecomment-2726939517  

Yes, Boost::unordered

---

## Comment 3

> Username: enginelesscc  
> Created at: 2025-03-15 19:06:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/307#issuecomment-2726941033  

The natvis does not work with either VS' native vcpkg integration nor cmake+vcpkg manifest

---

## Comment 4

> Username: cmazakas  
> Created at: 2025-03-16 14:41:14 UTC  
> Url: https://github.com/boostorg/unordered/issues/307#issuecomment-2727477105  

We're looking into this. Thanks for the report!

---

## Comment 5

> Username: cmazakas  
> Created at: 2025-03-24 15:01:49 UTC  
> Url: https://github.com/boostorg/unordered/issues/307#issuecomment-2748434514  

@enginelesscc   
  
Alright, did some investigation and I have some test ports, if you want to try them. Unfortunately, we're probably not going to be get these changes into the upcoming Boost release but if you could help us test, that'd be fantastic.  
  
I'm one of the core maintainers for this repo. I have a fork here with some overlay ports you can experiment with: https://github.com/cmazakas/unordered/tree/vcpkg-test/ports  
  
All you need to do is update your `vcpkg-configuration.json` to point to the [overlay-ports](https://learn.microsoft.com/en-us/vcpkg/reference/vcpkg-configuration-json) or if you're using classic mode vcpkg, there's always the `--overlay-ports` command line argument.  
  
These updated ports _should_ work. They worked for me in VS Code locally. Let me know if you try them out or need any help setting up the overlay ports. You'll need filesystem-local copies of these ports as my forked repository is not setup to act as a registry.

---

## Comment 6

> Username: enginelesscc  
> Created at: 2025-03-24 18:41:07 UTC  
> Updated at: 2025-03-24 18:41:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/307#issuecomment-2749084518  

Thanks for the fast investigation!  
Your changes are working properly :)  
  
@cmazakas
