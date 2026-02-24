# #929 - Remove standalone [Closed]

> Username: vinniefalco  
> Created at: 2023-01-26 16:40:38 UTC  
> Updated at: 2023-01-26 20:47:41 UTC  
> Closed at: 2023-01-26 20:27:09 UTC  
> Url: https://github.com/boostorg/math/issues/929  

Why should Boost authors and maintainers support two different versions of the library? Users can simply install Boost. If they cannot be bothered to install Boost then why should we support them?

---

## Comment 1

> Username: NAThompson  
> Created at: 2023-01-26 16:56:08 UTC  
> Updated at: 2023-01-26 17:10:31 UTC  
> Url: https://github.com/boostorg/math/issues/929#issuecomment-1405307435  

I don't think your mental model of how our users are consuming our library is correct. The main difference is that bulk of our customers are not GUI-style projects like ANSYS, where binaries are given to users, but more like scientific codes, where the entire codebase and all dependencies are delivered to users, who then source build the entire stack themselves, perhaps on a very custom HPC architecture. In particular:  
  
- Many of our users vendor boost.math header files. If they had to vendor all of boost it would be impractical.  
- Many of them are using [spack](https://spack.io)-style environments where they need fast downloads of all their dependencies for CI tasks. CI downloads of all of boost are quite slow, especially if you only need boost.math.  
- The only non-standard boost release was the standalone beta. The rest are exactly what exists on releases. The support burden therefore does not seem to be unmanageable.  
- Most importantly, our users have been very clear that they do not like installing all of boost as a dependency for their projects.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-01-26 19:29:38 UTC  
> Url: https://github.com/boostorg/math/issues/929#issuecomment-1405499314  

Yes well, full disclosure my open issue is more symbolic rather than practical (for Math at least). However, something has to be done about the irrational fear of Boost. The model where everyone has to duplicate the functionality that would otherwise come from external dependencies is unsustainable. Ideally users could type "vcpkg install math" or whatever equivalent command for their package manager, and they would not "install all of boost."

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-01-26 19:30:29 UTC  
> Url: https://github.com/boostorg/math/issues/929#issuecomment-1405500927  

> I don't think your mental model of how our users are consuming our library is correct.  
  
This is very likely true

---

## Comment 4

> Username: NAThompson  
> Created at: 2023-01-26 20:23:17 UTC  
> Url: https://github.com/boostorg/math/issues/929#issuecomment-1405605971  

> However, something has to be done about the irrational fear of Boost.   
  
In my view (and again, context is that which is scarce, so understanding your mental model is important) the irrational fear comes from how large boost actually is, and how much extraneous functionality much be consumed to achieve ones more focused goals.  
  
> Ideally users could type "vcpkg install math" or whatever equivalent command for their package manager, and they would not "install all of boost."  
  
I have previously discussed with the conan team how to install each subpackage of boost. But it appears that must be done on our end.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-01-26 20:27:06 UTC  
> Url: https://github.com/boostorg/math/issues/929#issuecomment-1405609831  

As far as I know, something like `vcpkg install json` works (it only installs what you need). Math requires quite a lot though:  
  
![image](https://user-images.githubusercontent.com/1503976/214943174-64aefe09-32c6-471c-b6b0-dc9ff5f210f8.png)  
  
I will go ahead and close this because there is nothing actionable here, I only opened it to stimulate discussion (mainly out of frustration from users who are afraid to use Boost, a stubborn and recurring problem). Thanks for indulging me :)

---

## Comment 6

> Username: NAThompson  
> Created at: 2023-01-26 20:47:41 UTC  
> Url: https://github.com/boostorg/math/issues/929#issuecomment-1405630567  

> Math requires quite a lot though:  
  
Maybe we should open a ticket for `vpkg` to use the standalone . . .
