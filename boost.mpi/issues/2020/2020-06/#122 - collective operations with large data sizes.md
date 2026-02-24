# #122 - collective operations with large data sizes [Open]

> Username: francescopt  
> Created at: 2020-06-29 16:20:17 UTC  
> Updated at: 2020-07-03 09:38:01 UTC  
> Url: https://github.com/boostorg/mpi/issues/122  

The library crashes when performing a collective operation like ```gather``` when the size of the objects is very large, but that should be still manageable with supercomputers. The issue is not easy to reproduce, because it requires quite some memory available.  
  
The following programs illustrates this:  
  
```  
#include <iostream>  
#include <vector>  
#include <boost/serialization/vector.hpp>  
#include <boost/mpi/collectives.hpp>  
  
struct huge {  
  std::vector<unsigned char> data;  
  huge() : data(2ull << 30ull, 0) { }  
  
  template <class Archive>  
  void serialize(Archive& ar, const unsigned int version)  
  {  
    ar & data;  
  }  
};  
  
int main()  
{  
  boost::mpi::environment env;  
  boost::mpi::communicator world;  
  
  huge a{};  
  
  std::cout << world.rank() << " huge created "  << std::endl;  
  world.barrier();  
  
  if (world.rank() == 0)  
    {  
      std::vector<huge> all;  
      boost::mpi::gather(world, a, all, 0);  
    }  
  else  
    {  
      boost::mpi::gather(world, a, 0);  
    }  
  
  return 0;  
}  
```  
  
The program create an object of 1G of memory. The struct ```huge``` is defined so to force the library to have a non-primitive MPI type. When run with only 2 tasks, it crashes giving  
  
```  
terminate called after throwing an instance of 'std::length_error'  
  what():  vector::_M_range_insert  
```  
  
On a supercomputer JUWELS, which has boost 1.69, the error is:  
  
```  
mpi: /gpfs/software/juwels/stages/2019a/software/Boost/1.69.0-gpsmpi-2019a-Python-2.7.16/include/boost/mpi/allocator.hpp:142: T* boost::mpi::allocator<T>::allocate(boost::mpi::allocator<T>::size_type, boost::mpi::allocator<void>::const_pointer) [with T = char; boost::mpi::allocator<T>::pointer = char*; boost::mpi::allocator<T>::size_type = long unsigned int; boost::mpi::allocator<void>::const_pointer = const void*]: Assertion `_check_result == 0' failed.  
```  
It appears that the programs crashes around [this line](https://github.com/boostorg/mpi/blob/04743c9bfbbb4cb5c1f080728ad6db53b4a0a00a/include/boost/mpi/collectives/gather.hpp#L66) on ```gather.hpp```  
  
    oa << in_values[i];  
  
The same crash is found even running with 1 task.  
  
  
  
Reducing the size of ```huge``` as  
  
```  
struct huge {  
  std::vector<unsigned char> data;  
  huge() : data(2ull << 29ull, 0) { }  
  
   ....  
```  
  
makes again the program crash, this time it appears that the crash happens at [this line](https://github.com/boostorg/mpi/blob/04743c9bfbbb4cb5c1f080728ad6db53b4a0a00a/include/boost/mpi/collectives/gather.hpp#L81) of ```gather.hpp```  
  
```  
   packed_iarchive::buffer_type recv_buffer(is_root ? std::accumulate(oasizes.begin(), oasizes.end(), 0) : 0);  
```  
  
My impression is that the sizes are sometimes stored as ```int``` when they should be a ```size_t```. For example, in the line above, ```oasizes``` is a ```std::vector``` of ```int```: even if the single-object size fits into a ```int```, the total buffer of gathered objects could exceed 2^31.

---

## Comment 1

> Username: aminiussi  
> Created at: 2020-07-01 07:53:06 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652256293  

Ok, this is going to be tedious :neutral_face:

---

## Comment 2

> Username: aminiussi  
> Created at: 2020-07-01 10:11:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652328282  

Opened branch `bugfix/122-32b-buffer-size`

---

## Comment 3

> Username: aminiussi  
> Created at: 2020-07-01 10:42:48 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652342564  

@francescopt Can I use you test code in a test ?  
  
Thanks

---

## Comment 4

> Username: francescopt  
> Created at: 2020-07-01 10:44:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652343442  

> @francescopt Can I use you test code in a test ?  
  
sure, go ahead

---

## Comment 5

> Username: aminiussi  
> Created at: 2020-07-01 11:53:06 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652373685  

Things are more annoying than expected, since the 32 bit limit is an MPI standard limit (all buf sizes are specified as int).   
There might be a work-around (not using bytes as buffer types) but that won't be as trivial as replacing int with std::size_t in Boost.MPI.

---

## Comment 6

> Username: aminiussi  
> Created at: 2020-07-02 08:12:40 UTC  
> Updated at: 2020-07-02 08:13:02 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652858069  

The first "solution", since this problem appears for serialized type (I mean, for basic type, they're plain MPI issue, doesn't mean we should not deal with it at some point) and serialized types are typically not primitives. We could set a minimal archive size (like 8 or 16 bytes) and allocate the internal buffer with that type. To do that in a user friendly way (although we could templatize the slot size with a default value ?) we need a useful compromise between the maximum buffer size `(std::numeric_limits<int>::max*sizeof(slot)`) and the minimal serialized message size (`sizeof(slot)`).  
  
Any idea ?

---

## Comment 7

> Username: francescopt  
> Created at: 2020-07-02 11:52:07 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652960401  

I just found out that the issue of size of MPI data is apparently known, and there is a C library that address this issue: [BigMPI](https://github.com/jeffhammond/BigMPI). In the github repository there is also an academical paper discussing the issue.  
  
As for the minimum buffer size. I am not an expert, but could the page size be a good choice in terms of efficiency?

---

## Comment 8

> Username: aminiussi  
> Created at: 2020-07-02 11:56:56 UTC  
> Updated at: 2020-07-02 11:59:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652962423  

Page size looks a little big. Underlying implementation will use different  types of communication (with remote buffer on on send for small, two steps (size+workload) for bigger coms etc.)  
The question could be: what is a good default for the max message size. That default could be documented as modifiable in config.hpp.

---

## Comment 9

> Username: aminiussi  
> Created at: 2020-07-02 12:00:31 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-652963949  

The long term fix could be different, for example it could be based on Probe.

---

## Comment 10

> Username: francescopt  
> Created at: 2020-07-03 09:38:01 UTC  
> Url: https://github.com/boostorg/mpi/issues/122#issuecomment-653453627  

OK, I guess that this is more a matter of individual cases. For what I have in mind, a max size of 64 Gb would be OK, that would be a buffer size of 64 Gb / (2^31) = 32 bytes.  
  
I tried to experiment the maximum size of scattered data with ```MPI_Scatter``` on a cluster I have access to. The test is done by defining custom MPI types with ```MPI_Type_contiguous``` or ```MPI_Type_create_struct```: in both cases the program crashes on ```MPI_Scatter``` when the total size exceeds 2 Gb. So I wonder if there is a more fundamental limitation...
