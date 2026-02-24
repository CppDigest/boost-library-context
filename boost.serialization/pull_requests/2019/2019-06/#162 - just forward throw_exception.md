# #162 just forward throw_exception [Closed]

> Username: HDembinski  
> Created at: 2019-06-07 11:08:33 UTC  
> Updated at: 2020-06-24 18:14:27 UTC  
> Closed at: 2020-06-24 18:14:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/162  

This very simple implementation fixes the problem I have in Boost.Histogram when I try to compile my unit tests which use Boost.Serialization with `<exception-handling>off`. It also simplifies this code. `boost::serialization::throw_exception` is just forwarded to `boost::throw_exception`, which already calls `throw` if BOOST_NO_EXCEPTION is not defined and a user-defined `throw_exception` function otherwise.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2019-06-07 20:59:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/162#issuecomment-500036919  

@robertramey Now I am pretty sure the patch is correct, but the gcc-based appveyor builds still fail. But I don't see where. When my builds fail, I search for "error " but there is no error. I cannot find what is actually failing.

---

## Comment 2

> Username: robertramey  
> Created_at: 2019-06-07 21:07:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/162#issuecomment-500039040  

Yep - it's a bitch.  Here's what I do:  
a) I got the page with the failed test.  In this case it's https://ci.appveyor.com/project/RobertRamey/serialization-5b0xm/builds/25116591/job/i98kmjbhtf0qpg71  
b) I search the log for the text "...fail".    
c) this should show the failing test.  
  
I think it might display only the first 2000 lines of the log.  In this case you have to download the log onto your machine so you can search it.  
  
BTW - this is the same test/configuration which has been failing forever with dynamic linking on windows.  But I've never been able to correct it as I don't have a windows system either! With your change, it seems that the issues shows up with static linking as well.  
  
> ****

---

## Comment 3

> Username: glenfe  
> Created_at: 2019-09-04 15:04:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/162#issuecomment-527942778  

@robertramey the issues you're seeing on Appveyor (Windows) might have to do with long paths for the generated test binaries that exceed some path limit.  
```  
[00:11:52] The system cannot find the path specified.  
[00:11:52] The system cannot find the path specified.  
[00:11:52] The system cannot find the path specified.  
[00:11:52] The system cannot find the path specified.  
```

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-09-05 13:01:17 UTC  
> Url: https://github.com/boostorg/serialization/pull/162#issuecomment-528352093  

I'll help out with the Windows GCC Appveyor test issues after this NVP change is done.

---

## Comment 5

> Username: robertramey  
> Created_at: 2019-10-17 16:33:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/162#issuecomment-543257513  

FWIW - I've fixed the long test name which was triggering the bogus failure.  So at least that's address.  I haven't looked at throw exception in a long time.  I'm pretty sure I included the code to avoid calling a "upgrade" to boost exception which drew in all kinds of unexpected behavior and dependencies.  If I get some time I might revisit this decision.

---
