# #203 Compiler detection for LLVM/Clang with Visual Studio integration [Closed]

> Username: m-j-w  
> Created at: 2015-11-12 20:02:24 UTC  
> Updated at: 2015-11-16 19:07:04 UTC  
> Closed at: 2015-11-16 16:44:30 UTC  
> Url: https://github.com/boostorg/hana/pull/203  

Provided are some changes to the compiler detection with cmake.  
The invocation on the command line (cmd.exe) actually only requires for a 32bit build  
  
```  
cmake .. -TLLVM-vs2014  
```  
  
A 64bit build is generated with  
  
```  
cmake .. -TLLVM-vs2014 -G"Visual Studio 14 2015 Win64"  
```  
  
Other options should not be required on a clean cmake run. The additional comparisons are for the case when the initial invocation is missing the -T argument, and hence everything get's really messy. This happens in particular when an existing solution is changed afterwards, e.g. by invoking cmake again with other options, but might also happen by other manual changes in the solution and the automatically triggered cmake run.  
  
The check for MSVC must be the first. Otherwise the MSC compiler will fail at the first `#warning`. MSC uses a `#pragma message` for the same purpose.  
The standard library of MSVC can be checked by the compiler version `_MSC_VER` since its bundled. However, detecting MSVC is not sufficient; `_clang_` must also be present. Unfortunately, also this might not be enough since the compiler could be changed manually, hence the platform toolset must also start with `LLVM-vs...` to ensure the correct MSVC-internally provided configuration with the LLVM/Clang integration.

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-11-13 21:11:57 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156557941  

For reference, this pull-request is related to issue #201.

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-11-15 16:20:21 UTC  
> Updated_at: 2015-11-15 16:20:37 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156825527  

Once the CI passes, could you please squash the three first commits together (since they are just reworks one of another), and I'll merge this.

---

## Comment 3

> Username: ldionne  
> Created_at: 2015-11-15 16:21:35 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156825648  

Oh yes, and if you could rebase on top of the current `develop` branch, it would be really nice.

---

## Comment 4

> Username: m-j-w  
> Created_at: 2015-11-15 17:10:27 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156831158  

Hmm, that didn't work as expected...

---

## Comment 5

> Username: ldionne  
> Created_at: 2015-11-15 17:12:29 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156831393  

If the shit hits the fan, just leave the commits as-is and I'll squash + rebase them as needed :-)

---

## Comment 6

> Username: m-j-w  
> Created_at: 2015-11-15 17:29:32 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156835704  

Well, I should clean that up later. At some point I managed to push my squashed changes to my develop branch.  
So just take that squashed commit db9daa69aff2c17cf37841f8d3b38644434cdfc0 on my develop branch. It should merge with your develop branch.

---

## Comment 7

> Username: ldionne  
> Created_at: 2015-11-15 17:31:13 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156836225  

> Well, I should clean that up later. At some point I managed to push my squashed changes to my develop branch.  
  
You want to push to your `windows_build` branch, not your `develop` branch. Pushing to your `windows_build` branch will update this pull request.

---

## Comment 8

> Username: m-j-w  
> Created_at: 2015-11-15 17:46:58 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156837837  

Yeah, well, adding the + sign before the branch to enforce the push after squashing also helps a lot...

---

## Comment 9

> Username: ldionne  
> Created_at: 2015-11-15 17:49:11 UTC  
> Url: https://github.com/boostorg/hana/pull/203#issuecomment-156838071  

> Yeah, well, adding the + sign before the branch to enforce the push after squashing also helps a lot...  
  
You can also use `git push -f` or `git push --force`

---
