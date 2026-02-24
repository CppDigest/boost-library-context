# #120 - boot mpi python module name [Open]

> Username: aminiussi  
> Created at: 2020-06-19 08:05:10 UTC  
> Updated at: 2020-06-23 07:28:45 UTC  
> Url: https://github.com/boostorg/mpi/issues/120  

Right now it's `mpi`, which is kind of... bad.  
  
We should at least have `boost` at the module path root

---

## Comment 1

> Username: aminiussi  
> Created at: 2020-06-19 08:06:19 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-646501398  

@xnox : Let's move this one here

---

## Comment 2

> Username: aminiussi  
> Created at: 2020-06-22 10:32:38 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647432401  

Maybe we can export both **mpi** (to be inline with the current name) and **boost.mpi**  
  
**mpi** should probably be made obsolete.

---

## Comment 3

> Username: xnox  
> Created at: 2020-06-22 11:25:27 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647456478  

But like in debian it has always been `boost/mpi`, i think it's a recent regression, or maybe you are running things out of the build-tree, rather than the install tree?  
  
https://packages.debian.org/stretch/amd64/libboost-mpi-python1.62.0/filelist  
https://packages.debian.org/sid/amd64/libboost-mpi-python1.67.0/filelist  
https://packages.debian.org/sid/amd64/libboost-mpi-python1.71.0/filelist  
  
Or is that just a debian/ubuntu thing?

---

## Comment 4

> Username: aminiussi  
> Created at: 2020-06-22 11:33:57 UTC  
> Updated at: 2020-06-22 11:34:18 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647460268  

That's probably the problem. When building from the git repository, the mpi.so file is droped in `./stage/lib/boost-python2.7/`.  
  
That should be in `./stage/lib/boost-python2.7/boost` with he appropriate `__init__.py`.  
  
I have no idea how to do that with bjam

---

## Comment 5

> Username: aminiussi  
> Created at: 2020-06-22 11:36:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647461345  

On my ubuntu box:  
```  
alainm@jarvis:~/views/boostorg/boost$ find /opt/boost-1.73.0/lib/ -name mpi.so  
/opt/boost-1.73.0/lib/boost-python3.6/mpi.so  
alainm@jarvis:~/views/boostorg/boost$   
```

---

## Comment 6

> Username: aminiussi  
> Created at: 2020-06-22 14:03:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647540018  

Ok, I don't know if you had a chance to look at #112, but the issue has been discussed there.  
  
At this point, it's a manual packaging issue.

---

## Comment 7

> Username: xnox  
> Created at: 2020-06-22 14:11:02 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647544424  

> Ok, I don't know if you had a chance to look at #112, but the issue has been discussed there.  
>   
> At this point, it's a manual packaging issue.  
  
Ubuntu & Debian python enable multiarch qualified names for extensions. Thus on Debian/Ubuntu compiled extensions have qualified names, such as mpi.cpython-35m-x86_64-linux-gnu.so which encode the cpython major/minor, subtype (m), and architecture.  
  
We do create build directly per python version, and then rename the modules later.  
  
mpi.so must not be shipped in /lib/ at all. That's not how one ships compiled python extensions.....

---

## Comment 8

> Username: aminiussi  
> Created at: 2020-06-22 14:18:43 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647549014  

Sure, but I don't think we have much control on the way it's shipped at this point.   
  
I'm not even sure where to find the concerned bjam section.  
  
Right now, apart from a but of documentation that says `"mpi.so is in <dist>/lib/, be careful to rename it and move it in the appropriate place"`, I'm not sure of what can be done....

---

## Comment 9

> Username: aminiussi  
> Created at: 2020-06-22 15:46:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647607425  

I added some doc in 389fbf3f1101d2972c9b46a4c282a8222b35c799 until we come up with something better.

---

## Comment 10

> Username: xnox  
> Created at: 2020-06-22 21:12:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647771979  

Sigh ok. The right thing to do is to query python's configuration (by calling interpreter, or using pyconfig, or the pkg-config files) figure out how to call extensions, and where to place them, and then rename & place them appropriately.  
  
That should do the right thing, for the right python. As things are called and places in slightly different ways depending on the python type and platform it is on.

---

## Comment 11

> Username: aminiussi  
> Created at: 2020-06-23 07:28:44 UTC  
> Url: https://github.com/boostorg/mpi/issues/120#issuecomment-647963066  

> The right thing to do is to query python's configuration (by calling interpreter, or using pyconfig, or the pkg-config files) figure out how to call extensions, and where to place them, and then rename & place them appropriately.  
  
If you could provide a script that does that, maybe we could discuss in issue #112 if it could be added in the distribution ?
