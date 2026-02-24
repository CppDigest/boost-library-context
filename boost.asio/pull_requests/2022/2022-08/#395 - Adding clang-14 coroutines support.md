# #395 Adding clang-14 coroutines support [Closed]

> Username: ramblehead  
> Created at: 2022-08-20 00:55:23 UTC  
> Updated at: 2022-10-15 22:35:24 UTC  
> Closed at: 2022-10-15 22:33:24 UTC  
> Url: https://github.com/boostorg/asio/pull/395  

In clang-14 ```<experimental/coroutine>``` is replaced by ```<coroutine>```, thus need to add an additional clang version test macro to set ```BOOST_ASIO_HAS_CO_AWAIT``` correctly.

---

## Comment 1

> Username: raldone01  
> Created_at: 2022-10-12 07:05:44 UTC  
> Url: https://github.com/boostorg/asio/pull/395#issuecomment-1275689580  

You should consider updating your fork and force pushing to only merge 1 commit not 186.

---

## Comment 2

> Username: ramblehead  
> Created_at: 2022-10-15 22:13:06 UTC  
> Updated_at: 2022-10-15 22:17:03 UTC  
> Url: https://github.com/boostorg/asio/pull/395#issuecomment-1279841591  

@raldone01 I am not sure why GH shows such history from 15 years ago in this pull request. I have not seen anything like this before. The following screenshot shows how it looks in my git repo (emacs magit):  
  
![Screenshot from 2022-10-15 22-56-50](https://user-images.githubusercontent.com/2130398/196009119-a3c1be9a-54e4-45ba-b7a9-ba7f11185949.png)  
  
Unless I do not understand something, my Subj. commit "sits" right on top of the ```upstream/master``` branch on ```boost-1.80.0``` release tag (```upstream == "git@github.com:boostorg/asio.git"```).  
  
I suspect, it might be a GH PR web interface bug due to complex merge history. This is how repo history looks in magit when scrolled a little down:  
![Screenshot from 2022-10-15 23-04-34](https://user-images.githubusercontent.com/2130398/196009301-bd48c5db-eec5-4e10-8268-5019ea2a2d06.png)  
  
Let me know if you have a better idea of why it is happening.

---

## Comment 3

> Username: ramblehead  
> Created_at: 2022-10-15 22:23:36 UTC  
> Updated_at: 2022-10-15 22:35:24 UTC  
> Url: https://github.com/boostorg/asio/pull/395#issuecomment-1279843009  

I just realised after writing the above comment that I am trying to create PR from a branch that is based on "master" into "develop" branch...  
  
I will try to create another PR that is based on "develop" branch...

---

## Comment 4

> Username: ramblehead  
> Created_at: 2022-10-15 22:33:24 UTC  
> Url: https://github.com/boostorg/asio/pull/395#issuecomment-1279844148  

I created another PR by cherry-picking Subj. commit onto "develop" branch. The history looks clean now: #398  
  
Closing this PR - use #398 instead...

---
