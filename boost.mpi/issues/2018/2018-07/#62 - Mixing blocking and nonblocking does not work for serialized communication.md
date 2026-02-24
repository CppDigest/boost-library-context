# #62 - Mixing blocking and nonblocking does not work for serialized communication [Closed]

> Username: hirschsn  
> Created at: 2018-07-03 08:13:10 UTC  
> Updated at: 2018-08-06 21:57:51 UTC  
> Closed at: 2018-08-06 21:57:51 UTC  
> Url: https://github.com/boostorg/mpi/issues/62  

Mixing blocking and nonblocking sends and receives (send + irecv and isend + recv) does not work for serialized communication (boost 1.67.0, g++ 7.3 and OpenMPI 2.1.1, also for newer versions of OpenMPI). The MPI Standard explicitly allows this (§3.7).  
  
Take this example:  
```c++  
#include <vector>  
#include <iostream>  
#include <iterator>  
#include <boost/mpi.hpp>  
#include <boost/serialization/vector.hpp>  
  
namespace mpi = boost::mpi;  
  
int main(int argc, char **argv)  
{  
    bool iswap = argc > 1 && argv[1] == std::string("iswap");  
  
    mpi::environment env(argc, argv);  
    mpi::communicator comm_world;  
    auto rank = comm_world.rank();  
  
    if (rank == 0) {  
        std::vector<int> data;  
        if (iswap) {  
            auto req = comm_world.irecv(1, 0, data);  
            req.wait();  
        } else {  
            comm_world.recv(1, 0, data);  
        }  
        std::cout << "Process 0 received:" << std::endl;  
        std::copy(std::begin(data), std::end(data), std::ostream_iterator<int>(std::cout, " "));  
        std::cout << std::endl;  
  
    } else if (rank == 1) {  
        std::vector<int> vec = {1, 2, 3, 4, 5};  
        if (iswap) {  
            comm_world.send(0, 0, vec);  
        } else {  
            auto req = comm_world.isend(0, 0, vec);  
            req.wait();  
        }  
    }   
}  
```  
  
It matches an isend with a recv (or a send with an irecv if you pass `iswap` as an argument). What happens is this:  
```sh  
$ mpic++ serialized-mixture.cc -o serialized-mixture -lboost_mpi -lboost_serialization  
$ mpiexec -n 2 ./serialized-anysource-2proc  
Process 0 received:  
5 0 1 2 3 4 5 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0  
$ mpiexec -n 2 ./serialized-mixture iswap  
[lapsgs17:32755] *** Process received signal ***  
[lapsgs17:32755] Signal: Aborted (6)  
[lapsgs17:32755] Signal code:  (-6)  
[lapsgs17:32755] [ 0] /lib/x86_64-linux-gnu/libc.so.6(+0xterminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::mpi::exception> >'  
  what():  MPI_Wait: MPI_ERR_TRUNCATE: message truncated  
3ef20)[0x7f1b3f93bf20]  
[lapsgs17:32755] [ 1] /lib/x86_64-linux-gnu/libc.so.6(gsignal+0xc7)[0x7f1b3f93be97]  
[lapsgs17:32755] [ 2] /lib/x86_64-linux-gnu/libc.so.6(abort+0x141)[0x7f1b3f93d801]  
[lapsgs17:32755] [ 3] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x8c8fb)[0x7f1b3ff928fb]  
[lapsgs17:32755] [ 4] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x92d3a)[0x7f1b3ff98d3a]  
[lapsgs17:32755] [ 5] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x92d95)[0x7f1b3ff98d95]  
[lapsgs17:32755] [ 6] /usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0x92fe8)[0x7f1b3ff98fe8]  
[lapsgs17:32755] [ 7] ./serialized-mixture(_ZN5boost15throw_exceptionINS_3mpi9exceptionEEEvRKT_+0x84)[0x55fc8d374c07]  
[lapsgs17:32755] [ 8] ./serialized-mixture(+0x187d9)[0x55fc8d3777d9]  
[lapsgs17:32755] [ 9] /usr/lib/x86_64-linux-gnu/libboost_mpi.so.1.65.1(_ZN5boost3mpi7request4waitEv+0x32)[0x7f1b40a02402]  
[lapsgs17:32755] [10] ./serialized-mixture(+0xf9a2)[0x55fc8d36e9a2]  
[lapsgs17:32755] [11] /lib/x86_64-linux-gnu/libc.so.6(__libc_start_main+0xe7)[0x7f1b3f91eb97]  
[lapsgs17:32755] [12] ./serialized-mixture(+0xf75a)[0x55fc8d36e75a]  
[lapsgs17:32755] *** End of error message ***  
--------------------------------------------------------------------------  
mpiexec noticed that process rank 0 with PID 0 on node lapsgs17 exited on signal 6 (Aborted).  
--------------------------------------------------------------------------  
```  
  
Note that if you do this with PODs, it works (and I suspect it will also work if boost::mpi can map the data directly to an MPI datatype, but I have not tried this case). Also, of course it works if you match isends with irecvs, etc. (change one of the `if (iswap)` to `if (!iswap)`).  
  
I could not find a spot in the boost::mpi doc that mentions that boost::mpi does not support mixing blocking and nonblocking calls. Nevertheless, I think an incompatibility with MPI itself in this matter should be prevented.  
  
When I have time, I can do some digging into this matter, for now I just wanted it to be documented.

---

## Comment 1

> Username: hirschsn  
> Created at: 2018-07-04 15:10:25 UTC  
> Updated at: 2018-07-04 15:10:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-402505680  

Okay, this is due to the fact that send and recv have overloads for std::vector. If the datatype inside the vector has an associated MPI datatype, these functions actually send and receive in send_vector and recv_vector (communication.hpp). Isend and irecv do not have these implementations, so they dispatch to the packed_oarchive_impl.

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-07-04 18:08:18 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-402536697  

So adding the overload would do the trick, although I'm not sure how much of a good idea the overload are in the first place. They were supposed to be an optimization that would provide the same performances as raw arrays+explicit size communication, but make the assumption the the receiver knows the size.

---

## Comment 3

> Username: hirschsn  
> Created at: 2018-07-05 06:33:15 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-402619799  

Yes, adding these would fix the problem in my opinion.  
  
The send_vector implementations do also send the array size (send_vector from communicator.hpp):  
```c++  
template<typename T, typename A>  
void communicator::send_vector(int dest, int tag,   
  const std::vector<T,A>& value, mpl::true_ true_type) const  
{  
  // send the vector size  
  typename std::vector<T,A>::size_type size = value.size();  
  send(dest, tag, size);  
  // send the data  
  this->array_send_impl(dest, tag, value.data(), size, true_type);  
}  
```  
  
The optimization in my opinion is that the data does not need to run through boost::serialization and can be directly send as an array in `array_send_impl`. The packed send needs to serialize the data and send it as MPI_PACKED. Although I don't know the details, sending directly definitively seems to be an optimization for large data as I have MPI_Alloc_mem popping up in `ltrace` for the packed isend and irecv.

---

## Comment 4

> Username: aminiussi  
> Created at: 2018-07-31 11:14:59 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-409184617  

At some point, we introduced an optimization that was a bug and only partially implemented it, which made it worse.   
  
If T is a primitive enough type, serialization is not needed in send nor receive. *But* we should not assume that the size of the vector (number of elements) is known and send/receive that number first.  
  
If the user know that size (s)he should use the pair (value.data(), value.size()).  
  
I'm going to remove the size optimization and make that clear in the documentation.

---

## Comment 5

> Username: aminiussi  
> Created at: 2018-07-31 13:41:39 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-409224892  

My last explanation is wrong. The problem is the one you described.

---

## Comment 6

> Username: aminiussi  
> Created at: 2018-08-01 10:26:34 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-409528614  

The optimization we try to achieve is not as much to avoid serialization, as serialization of vectors of primitive types are implemented by memory copy, as to avoid the allocation of the underlying archive (vectors can be huge).  
  
So the current version of the branch (ebaf38e) send/recv vectors through archive for non primitives (as those need to be serialized anyway) which support mixing blocking and non blocking and a ad hoc send/recv implementation for primitive type which uses the vector as an archive, but without the copy.  
  
Not that elegant, and this might benefit some reorganization, but it seems to work.  
  
At some point, we might want something similar for valarray.  
  
The documentation needs to be updated to make it clear that vector are considered dynamic.

---

## Comment 7

> Username: aminiussi  
> Created at: 2018-08-06 21:57:49 UTC  
> Url: https://github.com/boostorg/mpi/issues/62#issuecomment-410865803  

merged #66
