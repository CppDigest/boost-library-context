# #58 measure charconv performance [Closed]

> Username: dvirtz  
> Created at: 2022-05-01 20:48:48 UTC  
> Updated at: 2022-06-17 22:02:01 UTC  
> Closed at: 2022-06-17 22:02:00 UTC  
> Url: https://github.com/boostorg/convert/pull/58  

Added `charconv` to performance test and updated documentation.  
I changed performance tests a bit to avoid overhead of calling `strlen` repeatedly as it's already known by `my_string`.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2022-05-02 00:02:58 UTC  
> Url: https://github.com/boostorg/convert/pull/58#issuecomment-1114372563  

Tnx. Appreciated. I am currently on LTS Ubuntu 20.04 with gcc-9 which can't compile charconv-related code and I can't run the test myself and verify the results. Especially the results related to "std::string vs. small-string" which might require review/change of the relevant chapter (about std::string overhead). So, I'll have to put this merge on hold for now. LTS Ubuntu 22.04 with gcc-11 is coming out any day now. So, I'll revisit when I get gcc-11. Tnx again.

---

## Comment 2

> Username: yet-another-user  
> Created_at: 2022-06-04 07:04:06 UTC  
> Url: https://github.com/boostorg/convert/pull/58#issuecomment-1146554160  

Just a quick note that I have not forgotten about the pull request. Still waiting for Ubuntu 22.04 to come out to test it all on the proper platform. I tried installing the later g++ supporting charconv but that did not go smoothly and I backed off. Apologies for the delay.

---

## Comment 3

> Username: yet-another-user  
> Created_at: 2022-06-17 22:02:00 UTC  
> Url: https://github.com/boostorg/convert/pull/58#issuecomment-1159260159  

Done. If all goes well, then should be in the coming 1.80

---
