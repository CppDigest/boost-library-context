# #36 Drone config [Merged]

> Username: sdarwin  
> Created at: 2020-12-31 15:50:15 UTC  
> Updated at: 2021-01-13 17:43:00 UTC  
> Merged at: 2021-01-13 17:43:00 UTC  
> Closed at: 2021-01-13 17:43:00 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/36  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-01-02 16:41:52 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/36#issuecomment-753496175  

Why does Drone need so many files. Other than the .star file, which is the equivalent to the .yml file in travis or appveyor, the rest really seem like a waste of time and overkill. Eight separate files just to do CI testing ? It seems the design of Drone needs a rethinking.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-01-02 17:01:11 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/36#issuecomment-753499838  

All those files could probably be merged into a single .drone.star file.  The reason for this file structure is that the .star file is returning json (equivalent to yaml).  The format of a bash script embedded inside of json is slightly inconvenient in terms of quotes and indentation.   So, move the scripts into their own file. Dealing with complexities such as "here" documents, multiline strings, is clearer, although that might not apply to preprocessor.

---

## Comment 3

> Username: eldiener  
> Created_at: 2021-01-02 18:40:47 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/36#issuecomment-753513891  

I would suggest a single bash file called 'drone.sh, which you call with different parameters to to what you are doing with the seven different bash scripts you currently have.

---

## Comment 4

> Username: eldiener  
> Created_at: 2021-01-02 21:25:17 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/36#issuecomment-753531629  

BTW Vinnie Falco mentioned on the Boost developers mailing list that you might be interested in helping out with the programming of the Boost website, and that he would contact you about it. Has that happened ? Do you have any interest ? Recently I added a meta-file JSON 'cxxstd' field, set to 03,11,14,17, or 20, to reflect the minimum C++ standard which a Boost library requires in order to be largely useful. The idea is to add this information as a separate displayable field to the Boost libraries documentation online. I thought I could do it because I had worked with PHP in the past, but I found it I was no longer very good at it and the design of the website code eluded me.

---

## Comment 5

> Username: sdarwin  
> Created_at: 2021-01-13 17:12:12 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/36#issuecomment-759591926  

@eldiener, per request, there are only two files: .drone.star and drone.sh

---
