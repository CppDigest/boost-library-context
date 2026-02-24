# #694 Update GitHub Actions CI file [Closed]

> Username: sdarwin  
> Created at: 2021-08-02 19:08:46 UTC  
> Updated at: 2021-08-02 19:39:48 UTC  
> Closed at: 2021-08-02 19:39:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/694  

The Ubuntu 16.04 environment is scheduled to be removed from GitHub Actions in September 2021. Migrate those jobs to Docker containers or Ubuntu 18.04. Also, fix a problem with pip package installation on older platforms.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2021-08-02 19:26:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/694#pullrequestreview-720523104  

> The Ubuntu 16.04 environment is scheduled to be removed from GitHub Actions in September 2021. Migrate those jobs to Docker containers or Ubuntu 18.04. Also, fix a problem with pip package installation on older platforms.  
  
Thanks, but neither of Ubuntu 16.04 or Python is used...

📁 .github/workflows/ci.yml

```diff
 319 |+             sudo wget https://bootstrap.pypa.io/pip/$python_version/get-pip.py
 320 |+             sudo python3 get-pip.py
 321 |+             sudo /usr/local/bin/pip install cmake
```

> Username: Kojoley  
> Created_at: 2021-08-02 19:24:28 UTC  
> Updated_at: 2021-08-02 19:26:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/694#discussion_r681215302  

I have not had a need in this for a long while, IIRC Python 2.7, 3.4+ comes with Pip, strange to see that nowadays.

> Username: sdarwin  
> Created_at: 2021-08-02 19:35:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/694#discussion_r681221278  

@Kojoley , "a long while" is exactly the clue.  This script allows ubuntu 14 and ubuntu 16 to continue functioning a little while longer.   Otherwise pip is broken.     
Also, even on newer OS's, pip is a separate package, available but not automatically installed on docker containers.

> Username: Kojoley  
> Created_at: 2021-08-02 19:39:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/694#discussion_r681223392  

It feels like the condition then should be `if: os == "ubuntu-16.04" or os == "ubuntu-14.04"`, but there is no 14.04 image and 16.04 will be removed soon, so there is no point in the change.


---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-08-02 19:39:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/694#issuecomment-891282645  

> Thanks, but neither of Ubuntu 16.04 or Python is used...  
  
Ah ha!   You're right.  
  
I have been rolling out the update to multiple repos, automatically.  Most still use Ubuntu-16.  Closing the PR then.

---
