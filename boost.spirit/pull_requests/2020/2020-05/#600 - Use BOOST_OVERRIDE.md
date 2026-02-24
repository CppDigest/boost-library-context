# #600 Use BOOST_OVERRIDE [Merged]

> Username: EugeneZelenko  
> Created at: 2020-05-23 20:45:28 UTC  
> Updated at: 2020-05-24 14:03:50 UTC  
> Merged at: 2020-05-24 12:31:30 UTC  
> Closed at: 2020-05-24 12:31:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/600  

Use BOOST_OVERRIDE to fix GCC -Wsuggest-override and Clang-tidy modernize-use-override warnings.  
Also fix Clang-tidy readability-container-size-empty and modernize-redundant-void-arg warnings.  
Alphabetical order of STL headers.  
  
Closes #587

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-05-23 23:09:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/600#issuecomment-633152099  

Thanks!  
  
GitHub does not recognize you as the commit author, either you used a wrong email or this one is not attached to your profile. I am leaving the PR open for a while to let you fix it if you wish.

---

## Comment 2

> Username: EugeneZelenko  
> Created_at: 2020-05-23 23:15:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/600#issuecomment-633152547  

How could I fix this problem? I definitely did same thing as in previous merge requests.  
  
Anyway, changes are minor, so there is not need for too much bureaucracy :-)

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-05-23 23:19:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/600#issuecomment-633152887  

https://help.github.com/en/github/committing-changes-to-your-project/why-are-my-commits-linked-to-the-wrong-user

---

## Comment 4

> Username: EugeneZelenko  
> Created_at: 2020-05-23 23:30:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/600#issuecomment-633153728  

I could only wonder why GitHub ask for user name/password during push :-)  
  
I set e-mail address, but push didn't do anything because of absence of changes. I don't think that introducing artificial ones is reasonable course of action :-)

---

## Comment 5

> Username: Kojoley  
> Created_at: 2020-05-23 23:48:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/600#issuecomment-633155131  

> I could only wonder why GitHub ask for user name/password during push :-)  
  
To authorize push permission.  
  
> I set e-mail address, but push didn't do anything because of absence of changes. I don't think that introducing artificial ones is reasonable course of action :-)  
  
You need to reattribute your commit (rewrite the history, yikes) and force-push the changes. `git commit --amend --reset-author --no-edit && git push --force` should do the trick.

---

## Comment 6

> Username: EugeneZelenko  
> Created_at: 2020-05-24 00:12:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/600#issuecomment-633157187  

Thank you for help! Magic spell worked :-)

---
