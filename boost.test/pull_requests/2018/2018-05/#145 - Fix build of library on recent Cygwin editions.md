# #145 Fix build of library on recent Cygwin editions [Closed]

> Username: jeking3  
> Created at: 2018-05-20 02:39:19 UTC  
> Updated at: 2019-04-18 23:46:40 UTC  
> Closed at: 2018-08-01 17:27:18 UTC  
> Url: https://github.com/boostorg/test/pull/145  

Without this fix you get the behavior in #144.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-06-19 08:28:48 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-398318411  

On the branch ``topic/PR-145-cygwin-fixes``, tests cycling.

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2018-06-20 03:38:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/145#pullrequestreview-130230346  

📁 build/Jamfile.v2

```diff
  15 |     : requirements <link>shared:<define>BOOST_TEST_DYN_LINK=1
  16 |                    <toolset>borland:<cxxflags>-w-8080
  17 |+                    <target-os>cygwin:<define>_POSIX_C_SOURCE=201112L
```

> Username: jeking3  
> Created_at: 2018-06-20 03:38:49 UTC  
> Updated_at: 2018-06-20 03:44:12 UTC  
> Url: https://github.com/boostorg/test/pull/145#discussion_r196640038  

This is the same fix I put into the appveyor.yml of all affected repositories that use the boost-ci framework.  
  
https://github.com/jeking3/boost-ci/blob/master/templates/appveyor.yml  
  
The only problem here is that the value should be 200112L and not 201112L

> Username: raffienficiaud  
> Created_at: 2018-06-20 09:16:48 UTC  
> Updated_at: 2018-06-20 09:16:49 UTC  
> Url: https://github.com/boostorg/test/pull/145#discussion_r196703824  

So what should I do for the bjam? `200112L` or `20112L` ?


---

## Comment 3

> Username: jeking3  
> Created_at: 2018-06-20 03:42:04 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-398614799  

Also do you want a full CI environment (same that now exists on pool, ptr_container, format, uuid, ...?)

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2018-06-20 09:15:37 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-398681677  

Why not, I have my internal CI on OSX/Linux/Win, and on Win only different versions of Visual. Cygwin might be good to have as well.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-06-20 12:56:50 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-398738928  

The point of putting CI here is to allow people to self-serve their own quality of pull requests and to reduce the amount of work it takes for your to qualify a change.  
  
I pulled this PR and a CI PR into my fork:  
  
https://github.com/jeking3/test/pull/1  
  
Appveyor: https://ci.appveyor.com/project/jeking3/test/build/1.0.6-develop  
Travis CI: https://travis-ci.org/jeking3/test/builds/394392360  
  
I haven't looked into the failures yet.  Regardless, this particular fix (in this PR) is what I have used to resolve cygwin build issues in the repositories I maintain and in the CMT repositories.  In those, the definition is performed in the appveyor script so that Boost.Test builds.

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2018-06-20 13:32:11 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-398749375  

You are referring to a CI that is well integrated to GitHub. In all cases, I am running my own tests on the feature branches before merging, the configuration is stable for some years. Said that, you are right that it is always extra work to add a new platform, and I thank you for the ones that you are currently deploying. Let me know once it is stable on your end.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2018-06-20 13:40:09 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-398751935  

Going back to the purpose of the PR: the diff of this is `<target-os>cygwin:<define>_POSIX_C_SOURCE=201112L`, while the configuration you have on your Appveyor is `<target-os>cygwin:<define>_POSIX_C_SOURCE=2001112L`. At the end, I am confused on which one to take, my CI is blind to this change, yours is not green

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2018-06-21 23:04:57 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-399270524  

Hi again,  
  
I would like to merge this branch asap. Would you please confirm which of the options is correct? `201112L` or `2001112L` ?

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-06-22 02:21:29 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-399300535  

2001 is correct  
  
On Thu, Jun 21, 2018, 7:05 PM Raffi Enficiaud <notifications@github.com>  
wrote:  
  
> Hi again,  
>  
> I would like to merge this branch asap. Would you please confirm which of  
> the options is correct? 201112L or 2001112L ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/test/pull/145#issuecomment-399270524>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbcE9_8Qi_dbobWmAguBwa7H7jKlaks5t_CaagaJpZM4UF7i9>  
> .  
>

---

## Comment 10

> Username: raffienficiaud  
> Created_at: 2018-06-22 11:52:18 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-399416914  

I just tried on a fresh install of cygwin, and with or without the patch, the code compiles without problem. Just wondering how to reproduce. Any hint?

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2018-08-01 17:27:18 UTC  
> Url: https://github.com/boostorg/test/pull/145#issuecomment-409655409  

Closing, merged to master/1.68.

---
