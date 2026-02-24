# #854  Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-01-23 15:56:31 UTC  
> Updated at: 2024-05-07 19:28:26 UTC  
> Merged at: 2024-05-07 19:28:25 UTC  
> Closed at: 2024-05-07 19:28:25 UTC  
> Url: https://github.com/boostorg/boost/pull/854  

This adds the minimal set of declarations to make it possible to have both legacy non-modular building work with new modular building. It allows incremental addition in libraries to get them ready for modular building.  
  
This is a temporary state though. After all libraries, tools, and testing build in this mixed mode another version of the jamroot will be needed to remove the legacy build handling.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-02-01 01:04:12 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-1920281978  

@mclow , @glenfe , any objections to this?

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2024-02-01 01:05:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/854#pullrequestreview-1855171390  

📁 Jamroot

```diff
 152 |- modules.poke : BOOST_BUILD_PATH : $(BOOST_ROOT)/libs/config/checks [ modules.peek : BOOST_BUILD_PATH ] ;
 154 |+ import-search $(BOOST_ROOT)/libs/config/checks ;
 155 |+ import-search $(BOOST_ROOT)/libs/predef/tools/checks ;
```

> Username: pdimov  
> Created_at: 2024-02-01 01:05:53 UTC  
> Url: https://github.com/boostorg/boost/pull/854#discussion_r1473671236  

Does this still work if `libs/predef` is empty? (In a partial checkout)

> Username: grafikrobot  
> Created_at: 2024-02-01 02:57:26 UTC  
> Updated_at: 2024-02-01 03:03:34 UTC  
> Url: https://github.com/boostorg/boost/pull/854#discussion_r1473737558  

> Does this still work if `libs/predef` is empty? (In a partial checkout)  
  
Yes, as it has the same effect as what it replaces. In that it just pokes `BOOST_BUILD_PATH`. In the future when individual libraries use `import-search /boost/predef/tools/check ;` instead it will work "better" as it will error at that line if it doesn't find the project reference and dir. Instead of erroring later when doing the `import predef ;`.  
  
Interesting that you didn't ask about it working or not if `libs/config` is empty. Which is also a possibility. ;-)

> Username: pdimov  
> Created_at: 2024-02-01 04:10:24 UTC  
> Url: https://github.com/boostorg/boost/pull/854#discussion_r1473771293  

I actually do remember an occasion on which `libs/config` was empty and things failed somewhere, which is why I was reminded to ask. :-)  
  
(That's why depinst always installs `libs/config`.)

> Username: pdimov  
> Created_at: 2024-02-01 04:14:35 UTC  
> Url: https://github.com/boostorg/boost/pull/854#discussion_r1473773184  

(But `libs/config` is needed for more than that - the architecture and address model checks are there. Which is another story; these aren't Boost-specific and need to be in b2 proper.)


---

## Comment 3

> Username: grafikrobot  
> Created_at: 2024-02-27 19:35:03 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-1967459738  

Preparing a different PR that allows for an easier to manage transition to replace this one.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2024-03-18 12:02:30 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-2003729882  

Close & open for CI to reset.

---

## Comment 5

> Username: mclow  
> Created_at: 2024-03-20 18:01:54 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-2010274883  

I've looked at this, and I don't think I know enough about Jamfiles to make reasonable comments.

---

## Comment 6

> Username: grafikrobot  
> Created_at: 2024-04-30 04:54:00 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-2084378863  

Okay, this is truly now ready. I've now made all the changes to support both not-modular, fully-modular, and everything in between. You can see the testing in these two repos:  
  
- https://github.com/grafikrobot/boostorg.boost/actions - Has this change and all the libs and tools modified with the regular boost-root.  
- https://github.com/grafikrobot/boost-b2-modular/actions/workflows/check-libs.yml - Has a root-less structure with all the libraries, and the boost_install tool as the above. But only with a `project-config.jam` as a fully-modular setup. Note the CI on this is not expected to pass. As it's trying to fully build everything blindly. And many libs have failing tests in the regular course.

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-05-01 16:37:01 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-2088738356  

The change of BOOST_VERSION from 1.85.0 to 1.86.0 is probably no longer necessary.

---

## Comment 8

> Username: grafikrobot  
> Created_at: 2024-05-01 16:40:27 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-2088742841  

> The change of BOOST_VERSION from 1.85.0 to 1.86.0 is probably no longer necessary.  
  
You could press the update/rebase button to resolve that. I don't get that button apparently.

---

## Comment 9

> Username: grafikrobot  
> Created_at: 2024-05-06 13:54:49 UTC  
> Url: https://github.com/boostorg/boost/pull/854#issuecomment-2096076515  

> > The change of BOOST_VERSION from 1.85.0 to 1.86.0 is probably no longer necessary.  
>   
> You could press the update/rebase button to resolve that. I don't get that button apparently.  
  
@pdimov manually merged current develop to resolve that.

---
