# #63 - Nonblocking communication with boost::mpi::any_source not working for serialized communication [Closed]

> Username: hirschsn  
> Created at: 2018-07-03 08:51:20 UTC  
> Updated at: 2018-09-10 15:24:49 UTC  
> Closed at: 2018-09-10 08:50:43 UTC  
> Url: https://github.com/boostorg/mpi/issues/63  

Posting more than two irecv with boost::mpi::any_source results in message truncation errors (boost 1.67.0, g++ 7.3, OpenMPI 2.1.1 and newer).   
  
Code:  
```c++  
  
#include <vector>  
#include <iostream>  
#include <iterator>  
#include <boost/mpi.hpp>  
#include <boost/serialization/vector.hpp>  
  
namespace mpi = boost::mpi;  
  
int main(int argc, char **argv)  
{  
    mpi::environment env(argc, argv);  
    mpi::communicator comm_world;  
    auto rank = comm_world.rank();  
    if (rank == 0) {  
        std::vector<boost::mpi::request> req;  
        std::vector<std::vector<int>> data(comm_world.size() - 1);  
        for (int i = 1; i < comm_world.size(); ++i) {  
            req.push_back(comm_world.irecv(mpi::any_source, 0, data[i - 1]));  
            //auto req = comm_world.irecv(mpi::any_source, 0, data[i - 1]);  
            //req.wait();  
        }  
        boost::mpi::wait_all(std::begin(req), std::end(req));  
  
        for (int i = 0; i < comm_world.size() - 1; ++i) {  
            std::cout << "Process 0 received:" << std::endl;  
            std::copy(std::begin(data[i]), std::end(data[i]), std::ostream_iterator<int>(std::cout, " "));  
            std::cout << std::endl;  
        }  
  
    } else {  
        std::vector<int> vec = {1, 2, 3, 4, 5};  
        auto req = comm_world.isend(0, 0, vec);  
        req.wait();  
    }  
}  
```  
  
Symptoms:  
```sh  
$ mpic++ serialized-anysource.cc -o serialized-anysource -lboost_mpi -lboost_serialization  
$ mpiexec -n 3 ./serialized-anysource  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::mpi::exception> >'  
  what():  MPI_Test: MPI_ERR_TRUNCATE: message truncated  
[lapsgs17:04854] *** Process received signal ***  
[lapsgs17:04854] Signal: Aborted (6)  
[lapsgs17:04854] Signal code:  (-6)  
[lapsgs17:04854] [ 0] /lib/x86_64-linux-gnu/libc.so.6(+0x3ef20)[0x7ff37f056f20]  
[lapsgs17:04854] [ 1] /lib/x86_64-linux-gnu/libc.so.6(gsignal+0xc7)[0x7ff37f056e97]  
[lapsgs17:04854] [ 2] /lib/x86_64-linux-gnu/libc.so.6(abort+0x141)[0x7ff37f058801]  
[lapsgs17:04854] [ 3] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x8c8fb)[0x7ff37f6ad8fb]  
[lapsgs17:04854] [ 4] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x92d3a)[0x7ff37f6b3d3a]  
[lapsgs17:04854] [ 5] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x92d95)[0x7ff37f6b3d95]  
[lapsgs17:04854] [ 6] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x92fe8)[0x7ff37f6b3fe8]  
[lapsgs17:04854] [ 7] ./serialized-anysource(_ZN5boost15throw_exceptionINS_3mpi9exceptionEEEvRKT_+0x84)[0x5597ecc8415f]  
[lapsgs17:04854] [ 8] ./serialized-anysource(+0x19c49)[0x5597ecc87c49]  
[lapsgs17:04854] [ 9] /usr/lib/x86_64-linux-gnu/libboost_mpi.so.1.65.1(_ZN5boost3mpi7request4testEv+0x35)[0x7ff38011d595]  
[lapsgs17:04854] [10] ./serialized-anysource(+0x16ae1)[0x5597ecc84ae1]  
[lapsgs17:04854] [11] ./serialized-anysource(+0xfa2c)[0x5597ecc7da2c]  
[lapsgs17:04854] [12] /lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xe7)[0x7ff37f039b97]  
[lapsgs17:04854] [13] ./serialized-anysource(+0xf79a)[0x5597ecc7d79a]  
[lapsgs17:04854] *** End of error message ***  
--------------------------------------------------------------------------  
mpiexec noticed that process rank 0 with PID 0 on node lapsgs17 exited on signal 6 (Aborted).  
--------------------------------------------------------------------------  
```  
  
If one uses the commented out code instead of pushing back the request, i.e. directly waiting for a request instead of deferring the wait, the code works. Also, again, for PODs the code is working properly.  
The symptoms could be explained easily if count and data messages use the same (user provided) tags. Then, count and data messages can get mixed up because of any_source (one irecv receives both counts and one receives both data messages). But this is only speculation. Again, I can do some investigation as soon as I find the time to do it.

---

## Comment 1

> Username: hirschsn  
> Created at: 2018-07-04 12:35:15 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-402466154  

The problem is indeed that the count and data messages get mixed up. There is no trivial fix for this problem in conjunction with boost::mpi::any_source. In my opinion there are two options: Use a different tag value for serialized isend and serialized irecv for the second message (a 1:1 mapping from the user-supplied tag value, which can be used for the first message) or forbid serialized irecv from boost::mpi::any_source completely.  
  
Using a different tag value: Applications are allowed to use tag values in the range 0 .. MPI_TAG_UB. Using anything else would be highly dependant on the implementation of the used mpi library.  
Using a different tag value from within 0 .. MPI_TAG_UB is possible and works, however, might lead to serious problems if an application using boost mpi chooses to actually use this very tag value for its messages.

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-08-01 11:22:08 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-409542024  

Maybe there is another possibility (need to investigate though): the status of the size message seems to provide the actual source. If true, maybe we can force the data request to wait for the same ?

---

## Comment 3

> Username: aminiussi  
> Created at: 2018-08-01 11:28:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-409543638  

Which is what we are already doing...  
```  
     // Wait for the count message to complete                                                                                              
      BOOST_MPI_CHECK_RESULT(MPI_Wait,  
                             (self->m_requests, &stat.m_status)); // status of the sise msg....  
      // Resize our buffer and get ready to receive its data                                                                                 
      data->ia.resize(data->count);  
      BOOST_MPI_CHECK_RESULT(MPI_Irecv,  
                             (data->ia.address(), data->ia.size(), MPI_PACKED,  
                              stat.source(), stat.tag(), // .. provides the source for the data  
                              MPI_Comm(data->comm), self->m_requests + 1));  
  
```

---

## Comment 4

> Username: aminiussi  
> Created at: 2018-08-01 11:50:19 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-409548309  

Ok so now I get it, sorry.

---

## Comment 5

> Username: aminiussi  
> Created at: 2018-08-01 11:59:22 UTC  
> Updated at: 2018-08-01 11:59:51 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-409550326  

There might be another solution. Maybe we can ask the user a second, usually optional, tag to the user and make it mandatory when using any_source.  
  
As fr now, the check would be at run time *unless* we turn it into a tag (struct any_source_t{} any_source;) so that the compiler can force a second tag.  The use would still be able to use then value MPI_ANY_SOURCE directly but then we could warn.

---

## Comment 6

> Username: aminiussi  
> Created at: 2018-08-01 12:22:10 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-409556058  

We need to merge https://github.com/boostorg/mpi/pull/66 before we proceed with this issue as it will impact that specific code.

---

## Comment 7

> Username: hirschsn  
> Created at: 2018-08-06 16:13:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-410763176  

Asking for a second tag on serialized non-blocking communication indeed seems to be a good possibility given the options.

---

## Comment 8

> Username: hirschsn  
> Created at: 2018-08-07 08:12:19 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-410972833  

I just discussed this issue with a colleague and we came to the conclusion that it will still not work, even with two user specified tags.  
  
Imagine the following: Rank 0 does two successive (patched to take two user tags) isends to rank 1 with tag values C and D (which the new implementation would use for count and data messages, respectively). Rank 1 issues two irecvs with matching tags C and D and stores the requests in r1 and r2. Now r1 matches the count message of the first isend and r2 matches the count message of the second isend because MPI guarantees message ordering. But if the user then for some reason called `test` or `wait` on r2 first, the data message receive of r2 would match the data of the first isend. This is due to the fact that the user did specified the same tag pair for both isends.

---

## Comment 9

> Username: aminiussi  
> Created at: 2018-08-07 08:47:56 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-410982708  

Correct, this need more thinking. Unfortunately I won't be able to work on it for the coming 10 days.  
  
We could (using a any_source type tag as proposed earlier) prohibit at compile time its usage on non atomic messages. But that would prevent people from using a feature that could still work with some reasonable precautions.

---

## Comment 10

> Username: matthiastroyer  
> Created at: 2018-08-10 13:37:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-412084699  

Another option that we discussed long ago was to use another communicator for the contents of the nonblocking message.   
  
Matthias  
  
> On Aug 6, 2018, at 17:13, Steffen Hirschmann <notifications@github.com> wrote:  
>   
> Asking for a second tag on serialized non-blocking communication indeed seems to be a good possibility given the options.  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 11

> Username: hirschsn  
> Created at: 2018-08-13 07:35:55 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-412432936  

I don't know what has been discussed in the past. Do you have a reference to prior discussions about this or related problems? Anyway, let me quickly note some thoughts on using different communicators.  
  
Using just one pre-dupped communicator (e.g. a second MPI_Comm as part of every boost::mpi::communicator) that is responsible for all data messages does not solve the problem described above (calling `test` or `wait` in a different order).  
  
Using a new communicator for every single nonblocking point-to-point communication should work, however is impossible to do on-the-fly because dup, split, etc. on communicators are collective operations. It would require predefining n^2 point-to-point communicators, wouldn't it?

---

## Comment 12

> Username: aminiussi  
> Created at: 2018-08-20 11:13:01 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-414280899  

Maybe it has been part of the past discussion, but what about implementing point-to-point serialized data without size mesage ? using `MPI_Probe` `MPI_IProbe` on the receive side ?

---

## Comment 13

> Username: hirschsn  
> Created at: 2018-08-20 12:28:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-414299698  

I think this would solve both issues: not being able to tell a) which messages are data counts and which messages carry the actual data and b) which count message belongs to which data message (on same source, tag pair). Simply because there will be no count messages anymore.  
  
However, doing it only for serialized data will probably not work, since there are other kinds of data sent as count + message (for example in the std::vector overloads). These would also need to use a communication scheme with only one message being sent.

---

## Comment 14

> Username: aminiussi  
> Created at: 2018-08-20 12:35:35 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-414301461  

Well, we can match serialized send with serialized receive, But dealing with vector is probably not a big issue anyway.  
  
I'm opening a new issue, as this seems more general.

---

## Comment 15

> Username: aminiussi  
> Created at: 2018-08-20 13:35:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-414318657  

You can follow #70 if interested in implementation.

---

## Comment 16

> Username: aminiussi  
> Created at: 2018-08-24 23:14:01 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-415905771  

So, a probe version is mostly working, but is limited by what is probably a Intel MPI bug.  
  
The following MPI only code works up to 15 process on my installation, but fails to find incoming messages starting with 16 processes.  
  
It works on my Open MPI installation.  
  
Could you try on your available platform ?  
  
Thanks.  
   
```                                                                             
#include <stdio.h>  
#include <mpi.h>  
  
int main(int argc, char* argv[])  
{  
  MPI_Init(&argc, &argv);  
  int rank, nproc;  
  MPI_Comm_rank(MPI_COMM_WORLD, &rank);  
  MPI_Comm_size(MPI_COMM_WORLD, &nproc);  
  int value = 42;  
  int input;  
  int next = (rank + 1) % nproc;  
  int prev = (rank + nproc - 1) % nproc;  
  int tag = 2;  
  MPI_Request sreq;  
  MPI_Isend(&value, 1, MPI_INT, next, tag, MPI_COMM_WORLD, &sreq);  
  int probe = 0;  
  int test  = 0;  
  MPI_Message msg;  
  do {  
    if (!test) {  
      MPI_Test(&sreq, &test, MPI_STATUS_IGNORE);  
      if (test) {  
        printf("Proc %i sent msg %i to Proc %i\n", rank, tag, next);  
      } else {  
        printf("Proc %i have not sent msg %i to Proc %i yet\n", rank, tag, next);  
      }  
    }  
    if (!probe) {  
      int err = MPI_Improbe(prev, tag,  
                            MPI_COMM_WORLD, &probe,  
                            &msg,  
                            MPI_STATUS_IGNORE);  
      if (probe)  
        printf("Proc %i got msg %i from proc %i\n", rank, tag, prev);  
      else  
        printf("Proc %i haven't got msg %i from proc %i yet\n", rank, tag, prev);  
    }  
  } while(probe == 0 || test == 0);  
  MPI_Finalize();  
  return 0;  
}  
```  
My installations are:  
```  
Intel(R) MPI Library for Linux* OS, Version 2018 Update 3 Build 20180411 (id: 18329)   
```  
```  
mpirun (Open MPI) 2.1.1  
```

---

## Comment 17

> Username: Belcourt  
> Created at: 2018-08-25 02:27:19 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-415925668  

Worked for me on MBP running Sierra with OpenMPI 3.1.1 and random numbers of cores up to 147:  
  
mpiexec --map-by socket:OVERSUBSCRIBE -n 147 a.out  
  
No hangs or other weirdness so probably okay.  
  
Noel  
  
On Aug 24, 2018, at 5:14 PM, Alain Miniussi <notifications@github.com<mailto:notifications@github.com>> wrote:  
  
  
#include <stdio.h>  
#include <mpi.h>  
  
int main(int argc, char* argv[])  
{  
  MPI_Init(&argc, &argv);  
  int rank, nproc;  
  MPI_Comm_rank(MPI_COMM_WORLD, &rank);  
  MPI_Comm_size(MPI_COMM_WORLD, &nproc);  
  int value = 42;  
  int input;  
  int next = (rank + 1) % nproc;  
  int prev = (rank + nproc - 1) % nproc;  
  int tag = 2;  
  MPI_Request sreq;  
  MPI_Isend(&value, 1, MPI_INT, next, tag, MPI_COMM_WORLD, &sreq);  
  int probe = 0;  
  int test  = 0;  
  MPI_Message msg;  
  do {  
    if (!test) {  
      MPI_Test(&sreq, &test, MPI_STATUS_IGNORE);  
      if (test) {  
        printf("Proc %i sent msg %i to Proc %i\n", rank, tag, next);  
      } else {  
        printf("Proc %i have not sent msg %i to Proc %i yet\n", rank, tag, next);  
      }  
    }  
    if (!probe) {  
      int err = MPI_Improbe(prev, tag,  
                            MPI_COMM_WORLD, &probe,  
                            &msg,  
                            MPI_STATUS_IGNORE);  
      if (probe)  
        printf("Proc %i got msg %i from proc %i\n", rank, tag, prev);  
      else  
        printf("Proc %i haven't got msg %i from proc %i yet\n", rank, tag, prev);  
    }  
  } while(probe == 0 || test == 0);  
  MPI_Finalize();  
  return 0;  
}

---

## Comment 18

> Username: hirschsn  
> Created at: 2018-08-27 09:32:44 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-416170946  

Works for me on OpenMPI-3.1.1 and some older versions. Also, the code seems okay.

---

## Comment 19

> Username: aminiussi  
> Created at: 2018-08-27 11:32:47 UTC  
> Updated at: 2018-08-28 16:31:24 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-416197526  

fails with (on a different Linux cluster occigen.cines.fr):  
```  
Intel(R) MPI Library for Linux* OS, Version 5.1.3 Build 20160601 (build id: 15562)  
Intel(R) MPI Library for Linux* OS, Version 2017 Update 2 Build 20170125 (id: 16752)  
Intel(R) MPI Library for Linux* OS, Version 2017 Build 20160721 (id: 15987)  
Intel(R) MPI Library for Linux* OS, Version 2018 Build 20170713 (id: 17594)  
Intel(R) MPI Library for Linux* OS, Version 2018 Update 1 Build 20171011 (id: 17941)  
```  
  
Fails, on the same cluster, (licallo.oca.eu) with  
```  
Intel(R) MPI Library for Linux* OS, Version 2017 Update 3 Build 20170405 (id: 17193)  
Intel(R) MPI Library for Linux* OS, Version 2018 Update 3 Build 20180411 (id: 18329)   
```

---

## Comment 20

> Username: hirschsn  
> Created at: 2018-08-28 07:58:20 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-416488096  

Could you test the code with an MPI_Mrecv before finalizing. I know it sounds paranoid but I think the program could be ill formed. The standard says in §8.5 (MPI 3.1, p. 357, l. 34ff, MPI_Finalize): "[Before calling MPI_Finalize a process] must locally complete all MPI operations that it initiated and must execute matching calls needed to complete MPI communications initiated by other processes. For example, [...] if the process is the target of a send, then it must post the matching receive; [...]".  
  
It could be that Intel MPI relies on this in the implementations you tested?

---

## Comment 21

> Username: aminiussi  
> Created at: 2018-08-28 14:48:25 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-416614894  

Thanks for the remark, the code would indeed be non conformant as such.  
This is the reduced version, the original (with same behaviour) has an MPI_Mrecv call after MPI_imprbe success (if any).   
Still the Intel MPI runtime has no way of knowing if a matching MPI_Mrecv will be called when it execute the MPI_Improbe (that's no decidable in the general case). It could trigger an undefined behaviour when executing the finalize if the matching  recv wasn't called (while complying with 8.5) but that is not what I observe: the MPI_Improbe is never (well, this can't be decided neither, but let's say not for a long time) successful and as a consequence, finalize is not called.  
I'm still going to update the test case for my Intel issue so to make it conformant.   Thanks!

---

## Comment 22

> Username: aminiussi  
> Created at: 2018-08-28 16:32:08 UTC  
> Updated at: 2018-08-28 16:33:53 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-416653562  

Please note that I fixed the result comment, it always fails with intel (I got my SLURMA parameters wrong).   
Tried some config with more than one node (to force communication layer to IB) without improvement...

---

## Comment 23

> Username: aminiussi  
> Created at: 2018-08-28 19:55:10 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-416719080  

So, it is an issue with Intel'MPI implementation that should be fixed in the soon available 2019 version.  
I guess we will need to conditionally compile the new feature/fix and put a word in the documentation.

---

## Comment 24

> Username: aminiussi  
> Created at: 2018-09-06 20:32:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-419231407  

@hirschsn is it ok for you to provide code under the boost software license ?  
  
I'd like to integrate your test case.

---

## Comment 25

> Username: aminiussi  
> Created at: 2018-09-10 08:50:43 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-419837378  

This issue seems fixed in #70.

---

## Comment 26

> Username: hirschsn  
> Created at: 2018-09-10 15:24:49 UTC  
> Url: https://github.com/boostorg/mpi/issues/63#issuecomment-419953486  

Yes, I'm okay with that.
