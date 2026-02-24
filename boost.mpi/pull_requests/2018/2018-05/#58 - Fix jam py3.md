# #58 Fix jam py3 [Merged]

> Username: xnox  
> Created at: 2018-05-03 11:29:08 UTC  
> Updated at: 2018-05-23 19:37:23 UTC  
> Merged at: 2018-05-23 09:57:37 UTC  
> Closed at: 2018-05-23 09:57:37 UTC  
> Url: https://github.com/boostorg/mpi/pull/58  

Just build mpi-python against current boost_python, not boostpython2|3  
  
Looks like boost_python has moved on and stopped providing  
boost_python2 and boost_python3 libraries. Possibly a regression in  
boost_python, or possibly new world order which mpi should adapt to.  
  
Fixes build: --with-python --with-mpi --python-buildid=py36 python=3.6

---

## Comment 1

> Username: xnox  
> Created_at: 2018-05-03 11:33:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-386265617  

I know that indenting is wrong, but the diff is easier to read/review this way.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2018-05-20 17:12:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-390496895  

So, unless I'm missing something, it seems we should go as described in boostorg/python#203.  
  
Gonna give it a try then.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-05-20 23:11:43 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-390520146  

This PR solves a different problem than what is described in https://github.com/boostorg/python/issues/203, but it is a prerequisite. Without this change Boost.MPI fails to build because Boost.Python has changed the library target names. After this has been fixed, we can work on unifying library naming schemes, which is what https://github.com/boostorg/python/issues/203 is about.

---

## Comment 4

> Username: aminiussi  
> Created_at: 2018-05-21 09:09:03 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-390597993  

Any reason to merge on master instead of develop ?

---

## Comment 5

> Username: aminiussi  
> Created_at: 2018-05-21 13:02:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-390647457  

It's merged on develop. Waiting for some  [tests to pass on develop](https://www.boost.org/development/tests/master/developer/mpi.html) and then merging on master.

---

## Comment 6

> Username: aminiussi  
> Created_at: 2018-05-23 09:59:15 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-391291906  

Hi, @Lastique, it's in master, I did not merge your PR directly but made it pass through develop to check the test where ok on other platforms.  
  
Thks

---

## Comment 7

> Username: Lastique  
> Created_at: 2018-05-23 11:32:27 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-391314433  

Thanks. Although, it's not my PR.

---

## Comment 8

> Username: aminiussi  
> Created_at: 2018-05-23 11:59:25 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-391320942  

Oups, sorry.

---

## Comment 9

> Username: xnox  
> Created_at: 2018-05-23 19:37:23 UTC  
> Url: https://github.com/boostorg/mpi/pull/58#issuecomment-391470871  

Thanks a lot! I have no idea about devel vs master branch differences, and I simply proposed something. Thank you for sorting out the branches merge targets et. al.

---
