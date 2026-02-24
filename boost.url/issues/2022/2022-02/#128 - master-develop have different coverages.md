# #128 - master/develop have different coverages? [Closed]

> Username: vinniefalco  
> Created at: 2022-02-22 06:23:14 UTC  
> Updated at: 2022-07-31 03:29:47 UTC  
> Closed at: 2022-07-31 03:29:47 UTC  
> Url: https://github.com/boostorg/url/issues/128  

The code is the same but the coverage percentage in the badges is different?  
![image](https://user-images.githubusercontent.com/1503976/155074634-820fbf81-4501-40b8-8414-2ad6f007abfd.png)

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-04-08 18:59:47 UTC  
> Url: https://github.com/boostorg/url/issues/128#issuecomment-1093247336  

This seems to be happening because codecov uses only the commit hash to refer to each commit. The commit is associated with a branch the first time it appears and this association is permanent after that.  
  
For instance, the the last commit to master (https://github.com/CPPAlliance/url/commit/d1e0e11ac97adf0efd4352a34d3e143c31004ab1) triggered [this workflow](https://drone.cpp.al/CPPAlliance/url/473/1/2) to upload the coverage data. The workflow gives us this link without any reference to master:  
  
```console  
[2022-03-28T18:57:12.815Z] ['info'] https://codecov.io/github/CPPAlliance/url/commit/d1e0e11ac97adf0efd4352a34d3e143c31004ab1  
```  
  
If we [go to the link](https://codecov.io/github/CPPAlliance/url/commit/d1e0e11ac97adf0efd4352a34d3e143c31004ab1), we see this commit hash is associated with the `develop` branch, which is the branch associated with this hash the first time codecov saw it.  
  
When we look at [the list of branches](https://app.codecov.io/gh/CPPAlliance/url/branches?page=3&order=-updatestamp), codecov considers that the last commit to `master` was 2 years ago.   
  
![image](https://user-images.githubusercontent.com/5369819/162504067-c6482d05-619c-4879-9b4e-8bebb38082d5.png)  
  
The [most recent commit](https://codecov.io/gh/CPPAlliance/url/commit/68ded7b9410755726258dada07417e1c47cf70f4/) covecov associated with `master` was "Update build scripts", which is probably the lastest commit that didn't go through `develop` before.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-04-08 19:07:22 UTC  
> Url: https://github.com/boostorg/url/issues/128#issuecomment-1093256456  

Please bring it to codecov support attention https://github.com/codecov

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-04-08 19:10:04 UTC  
> Url: https://github.com/boostorg/url/issues/128#issuecomment-1093259827  

I did a little research and I'm quite sure they already know about it. There's even a tutorial related to this on their website (I don't have the link right now). But I'll ask if there's some workaround or something we could do.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-07-31 03:29:47 UTC  
> Url: https://github.com/boostorg/url/issues/128#issuecomment-1200340486  

I'm not losing sleep over this.
