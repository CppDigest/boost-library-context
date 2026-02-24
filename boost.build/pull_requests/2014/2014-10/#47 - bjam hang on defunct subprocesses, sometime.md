# #47 bjam hang on defunct subprocesses, sometime. [Closed]

> Username: aminiussi  
> Created at: 2014-10-28 10:07:25 UTC  
> Updated at: 2021-10-02 22:20:40 UTC  
> Closed at: 2014-11-01 08:24:53 UTC  
> Url: https://github.com/boostorg/build/pull/47  

Sometime being **once**, to be honest. More specifically, [mpiexec.hydra](https://wiki.mpich.org/mpich/index.php/Using_the_Hydra_Process_Manager) from the Intel MPI distribution (version 5.?).  
This is triggered when running the regression test, since this is the only situation where the MPI launcher is supposed to be used.  
So it's quite specific. Still.  
  
The problem has been discussed in a [thread ob boost users list](http://boost.2283326.n4.nabble.com/bjam-hangs-on-select-in-develop-branch-td4668391.html). In a nutshell:  
It seems, or everything behaves as if, the _mpiexec.hydra_ program send the **SIGSTOP** signal to its parents. If an ancestor (say, here, _bjam/b2_) enter a select with no (as in _NULL_) timeout, that select will hang forever (or at least until the paused program receives a **SIGCONT**, as verified throught the shell and debugger).  
  
The proposed solution use a **0** value (as in {0, 0} _value_) for the select timeout parameter, making it work in pool mode, and interleaving a wakeup call to the sub processes. The subtask timeout itself is dealt with in the exec_wait function anyway since we explicitly kill the timed out tasks.  
  
I added some error notification since I suspect these should not be left unnoticed.  
  
Cheers!

---

## Comment 1

> Username: aminiussi  
> Created_at: 2014-10-31 09:42:55 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61237954  

Is there any chance to see this patch in 1.57 ? It's blocking the testing of Boost.MPI with Intel.  
  
Thanks

---

## Comment 2

> Username: vprus  
> Created_at: 2014-10-31 10:49:28 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61244502  

Hi,  
  
thanks for submitting a patch, but I have concerns about this solution. It looks as it b2 will now actively poll for events from children, possibly eating CPU like crazy. Also, sending SIGCONT to all children all the time seems like disproportionate solution to a single misbehaving tool.  
  
In fact, I would it's not b2 business at all - if the tool behaves like that, it's not different from the tool crashing, and we don't normally workaround broken tools. Could you complain to tool developers, or maybe hack the binary to not send SIGSTOP?

---

## Comment 3

> Username: Belcourt  
> Created_at: 2014-10-31 20:16:45 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61324327  

I agree with Volodya, I don't like this PR either.   Too many un-necessary changes and I'm not entirely sure we want to go to a polling model for select (heck, why use select at all)?  
  
FYI, I just started running some develop and master tests with Intel 15 and Intel MPI 4.1, should have results sometime over the next day or so.  If we hit problems running Intel MPI tests, I'll certainly fix those (and hopefully that will fix your problems as well).  
  
Hopefully your Intel versions are similar to what I'll be running?  Anyways, you can close this ticket.

---

## Comment 4

> Username: aminiussi  
> Created_at: 2014-11-01 08:24:50 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61361544  

Just to answer a few question:  
- cpu consumption: that's a legitimate concern, but could be easily tuned by using a non zero interval. I didn't not appears to be an issue when I tried it, so I left the 'poll'. I suspect that the scheduler pass the cpu to another process (if only the forked one).  
- why use select at all: I don't know, it was there so... I'm not sure I would have used fork/select but I only know linux/unix haven't been manipulating processes from C for a long time.  
- it's mpiexe.hydra problem, not a b2 problem: yes, but most build tools I know (ok, that's 2 of them, hardly sufficient to infer a general rule) deal with the world as it is. More specifically here, b2 is used to run the tests, and tests can fails in nasty way. But this is just a point of view.  
- it's only mpiexe.hydra...: sure, but intel's MPI seems to be on the rise in the HPC community, and yes, it's annoying, since they behave in slightly different way than the competition in many respect (the configuration of boost/b2 wrt Intel is a pain, same for cmake, auto tool, some weird in house build system at caltech..)  
- ..can't they fix it?: well, yes, they probably could. I did report the problem some time ago. But, to summarize what seems to be their point of view:  "well, that's only we bjam" (and that seems to be true, I couldn't find any trace of a similar issue without b2) and "we don't now much about bjam". It tried to ask if they were aware of their tool sending SIGSTOP it caller, but I suspect the issue has to move up to engineering to have the answer. So I'm still pushing on that side too, the issue is still open. And was a bit surprised that they didn't tried to build Boost.  
  But at best it won't be fixed since next release, in the meantime I have to deploy a non tested version of Boost.MPI on our clusters (well, I still can use my patched version).  
- the versions I'm using are Intel 15 and Intel MPI 5.0.1. They might have changed something wrt mpd (te default behavior for mpirun ?), but mpiexec.hydra is probably quite similar.

---

## Comment 5

> Username: Belcourt  
> Created_at: 2014-11-01 17:44:01 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61376327  

Hi Alain,  I think it's worth fixing b2 to handle failing test programs for sure.  I just need a little more time to consider how to do this better.  I apologize that I'm not able to get it fixed as quickly as you'd like,  As for building b2 with Intel, how is building b2 with Intel a pain?  As for mpiexec.hydra sending SIGSTOP, that's atypical and will have to read up on handling it.  Thanks for the info, that's helpful and I'll work to get a patch that fixes this issue.

---

## Comment 6

> Username: Belcourt  
> Created_at: 2014-11-01 18:25:27 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61377865  

So now I'm really confused.  I see that my Intel 15 iMPI 4.1 develop tests ran last night, no issues at all with either graph-parallel or MPI.  Further, I was just reading about mpiexec.hydra and, from what I can tell from the limited documentation, mpiexec (or mpirun) both call mpiexec.hydra under the covers so when the tests ran last night, it means they ran and terminated without hanging using mpiexec.hydra.  
  
Alain, I need more info to figure this out.  If what Intel says is true, that both mpiexec and mpirun call mpiexec.hydra under the covers, and this hangs for you with b2, then I'm stumped.  I rather expected to see the iMPI 4.1 parallel tests I ran last night hang and that would give me a reproducer I could debug.  What program are you running under mpiexec.hydra, that is, can you post the entire mpiexec command line, dump and sort the environment, can give me some idea what sort of cluster you're running on?  Sorry to be a pain but there's no chance I can fix this unless I can reproduce it.

---

## Comment 7

> Username: aminiussi  
> Created_at: 2014-11-02 01:03:53 UTC  
> Updated_at: 2014-11-02 01:20:07 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61390052  

There's no need to apologize, I have tons of stuff I'm not fixing as fast as people would like to.  
I have the same problem with mpirun, except the the defunct job is still mpiexec.hydra and the one waiting is mpirun (instead of the sh process generated by b2). It's like mpiexe.hydra is pause it's caller.  
Maybe I could ask for an account on our cluster ? would that be simpler for you ?  
In the meantime, I still have my patched version that allow me to run the tests.

---

## Comment 8

> Username: aminiussi  
> Created_at: 2014-11-02 09:40:26 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61398329  

By the way, I understand you ran with mpiexec or mpirun, does it mean that you launched the mpd daemon ?

---

## Comment 9

> Username: Belcourt  
> Created_at: 2014-11-02 19:55:09 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61420782  

Default b2 MPI is to run with mpirun, which according to Intel MPI documentation will start the hydra mpd daemon and terminate it after the run completes.  Now perhaps that's not what's actually happening, maybe it's running mpd rather than hydra.  Do you know if there's an easy way to tell what mpirun -np ... does under iMPI?

---

## Comment 10

> Username: aminiussi  
> Created_at: 2014-11-04 13:58:44 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61642625  

From my documentation (impi 5.0.x), you can set the   
I_MPI_PROCESS_MANAGER to hydra.  
  
FWIW, I used the folowing bit of bjam configuration:  
  
using mpi : : <include>/softs/intel/impi/5.0.1.035/intel64/include  
<library-path>/softs/intel/impi/5.0.1.035/intel64/lib/release  
<library-path>/softs/intel/impi/5.0.1.035/intel64/lib  
<dll-path>/softs/intel/impi/5.0.1.035/intel64/lib/release  
<dll-path>/softs/intel/impi/5.0.1.035/intel64/lib  
           <find-shared-library>mpifort  
           <find-shared-library>mpi  
           <find-shared-library>mpigi  
           <find-shared-library>dl  
           <find-shared-library>rt  
           <find-shared-library>pthread  : mpiexec.hydra ;  
  
```  
Selecting a Process Manager  
```  
  
The mpirun script uses the process manager specified by the   
I_MPI_PROCESS_MANAGER variable.  By default, mpirun selects the Hydra   
Process Manager.  Setting I_MPI_PROCESS_MANAGER to hydra will explicitly   
select Hydra, and setting it to mpd will explicitly select MPD.  The   
process manager can also be selected by directly calling the appropriate   
mpiexec file: mpiexec.hydra for Hydra or mpiexec for MPD.  
  
On 02/11/2014 20:55, Noel Belcourt wrote:  
  
> Default b2 MPI is to run with mpirun, which according to Intel MPI   
> documentation will start the hydra mpd daemon and terminate it after   
> the run completes. Now perhaps that's not what's actually happening,   
> maybe it's running mpd rather than hydra. Do you know if there's an   
> easy way to tell what mpirun -np ... does under iMPI?  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/build/pull/47#issuecomment-61420782.  
  
##   
  
---  
  
Alain

---

## Comment 11

> Username: aminiussi  
> Created_at: 2014-11-05 08:23:45 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-61773775  

I'm not sure if it's okay to comment on a closed issue, so I'm reopening. But feel free to do what's more convenient.

---

## Comment 12

> Username: jesslinn  
> Created_at: 2014-11-09 04:27:04 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-62290871  

I have been seeing the setpgid failure for a week or so on the b2 develop branch.  
  
The problem may be that execunix.c in the b2 engine source calls vfork instead of fork.  vfork(), unlike fork() shares the memory and stack with the parent until the execvp is called, so doing most anything before calling exec leads to undefined behavior.

---

## Comment 13

> Username: Belcourt  
> Created_at: 2014-11-09 22:48:33 UTC  
> Url: https://github.com/boostorg/build/pull/47#issuecomment-62323718  

Yes, I added the setpgid check a couple of weeks ago.  Well, fork/vfork is certainly an issue that we've discussed before.  While we could shift to using fork, it's very inefficient and really slows builds down (especially for those of us who have other projects that use BB).  Thanks for you comment, we're looking at making some changes to fix BB but it may take a while.

---
