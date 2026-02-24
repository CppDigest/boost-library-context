# #32 Add a function for obtaining complete MPI transfer info of a variable [Closed]

> Username: nasailja  
> Created at: 2016-01-23 22:41:08 UTC  
> Updated at: 2016-08-06 01:34:54 UTC  
> Closed at: 2016-08-06 01:34:54 UTC  
> Url: https://github.com/boostorg/mpi/pull/32  

Is something like this suitable for boost::mpi? I have a few projects which need this functionality and boost::mpi seems like a good place as I'm guessing others might also find it useful.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2016-07-17 21:23:51 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233205027  

Well, there's already this:  
template<typename T> MPI_Datatype get_mpi_datatype(const T& x)  
{  
  BOOST_MPL_ASSERT((is_mpi_datatype<T>));  
  return detail::mpi_datatype_cache().datatype(x);  
}  
  
so these methods don't really seem to add any additional capability:  
  
tuple<const void*, int, MPI_Datatype> get_datatype(const int& x)  
tuple<const void*, int, MPI_Datatype> get_datatype(const std::vector<int>& x)  
  
Perhaps I'm missing something,  but I don't see the utility of these methods in MPI, though certainly I can see how a code might want to do something like this.  I don't think this belongs in MPI so while I appreciate your submission, I won't apply this PR.

---

## Comment 2

> Username: nasailja  
> Created_at: 2016-07-18 13:22:40 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233326575  

Boost version is missing 2/3 of the information required to send the data using MPI, namely the address from where to send data and number of data types to send. The proposed function(s) would return all required info.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2016-07-18 22:53:12 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233483152  

Perhaps I need to see how you're using this, I'm not clear how this helps me.  Can you post a self-contained test case where I can see your use of get_datatype in a broader context?  Also, what version of MPI are you using, on what system, compiler?

---

## Comment 4

> Username: nasailja  
> Created_at: 2016-07-19 01:03:03 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233503009  

A complete program (game of life in 1d) is available in listing 3 of http://www.geosci-model-dev.net/8/473/2015/gmd-8-473-2015.pdf which should work with MPI >= 1.0 and any C++11 compiler if you change the type of is_alive to int instead of bool. Using boost::tuple instead would make it also work with any C++ compiler I guess. (Below is a link to an email discussion of boost::mpi performance problem where I used boost-1.57.0, g++ (GCC) 4.8.3 20140911 (Red Hat 4.8.3-7) and Open MPI 1.6.5.) Here's what I'm currently using: https://github.com/nasailja/gensimcell/blob/master/source/get_var_mpi_datatype.hpp#L98 which is not much different from the boost version for basic types but I'm not sure about STL containers, see the link below which indicates that boost::mpi does create unnecessary copies of data when sending e.g. vector<double>.  
  
Now it's true that when using boost::mpi to transfer things I don't need the address or count from boost::get_mpi_datatype but that leads to my main problem with boost::mpi, namely that as long as I've tried to use it (6-7 years now) it has always been way too slow in transferring things for any serious high performance computing. See this for an example: http://lists.boost.org/boost-users/2015/02/83807.php . An easy solution would be to extend boost::get_mpi_datatype, or add a new function, which would only return a description of the data for MPI functions and not (potentially) copy the data or (try to) cache the datatype or anything else. Those who find boost::mpi too slow for transferring things could still use boost::mpi::get_datatype to obtain in C++ the information required by MPI functions in a portable, tested and widely available manner and use MPI functions for the transfers.

---

## Comment 5

> Username: Belcourt  
> Created_at: 2016-07-19 05:09:24 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233533756  

On MBP with:  
  
Apple LLVM version 7.0.2 (clang-700.1.81)  
Open MPI repo revision: v1.8.3-330-g0344f04  
  
compiled with Boost develop and:  
mpicxx -std=c++11 -O3 -I Projects/local/boost/include test.cpp -L Projects/local/boost/lib -l boost_mpi -l boost_serialization -Wl,-rpath /Users/kbelco/Projects/local/boost/lib  
  
I get this performance of your test.cpp test case.  
  
vector with 1e6 items:  
mpi  
0 resize: 0.00280419, send: 0.00101461, recv: 0  
1 resize: 0.00270515, send: 0, recv: 0.00114248  
boost::mpi  
0 resize: 0.000729277, send: 0.00239165, recv: 0  
1 resize: 0, send: 0, recv: 0.0031936  
  
vector with 1e7 items:  
mpi  
0 resize: 0.0292156, send: 0.012519, recv: 0  
1 resize: 0.0279033, send: 0, recv: 0.0138616  
boost::mpi  
0 resize: 0.00580257, send: 0.0137896, recv: 0  
1 resize: 0, send: 0, recv: 0.019648  
  
vector with 1e8 items  
mpi  
0 resize: 0.283473, send: 0.117936, recv: 0  
1 resize: 0.281631, send: 0, recv: 0.119808  
boost::mpi  
0 resize: 0.0499439, send: 0.163692, recv: 0  
1 resize: 0, send: 0, recv: 0.213715  
  
Still a factor of 2 - 3 times slower, but much better than order of magnitude.  If this looks acceptable, I can send you the patch so you can try it out.

---

## Comment 6

> Username: nasailja  
> Created_at: 2016-07-19 14:02:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233642599  

Sure, I can try it.

---

## Comment 7

> Username: Belcourt  
> Created_at: 2016-07-19 18:15:55 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233719661  

Only tested with std::vector<pod>, have implemented but not tested vector<udt>.  Looks like github won't let me attached a hpp so I've renamed communicator.hpp to communicator.txt.  Rename the file, drop it into your boost mpi installation.  Be curious if it works for you.  
[communicator.txt](https://github.com/boostorg/mpi/files/372138/communicator.txt)

---

## Comment 8

> Username: Belcourt  
> Created_at: 2016-07-19 18:17:33 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233720191  

Note that my previous comment was edited to remove some important information.  Code has only been tested with std vector of pod types, and has not been tested with std vector of udt (user defined types).

---

## Comment 9

> Username: nasailja  
> Created_at: 2016-07-19 18:37:41 UTC  
> Updated_at: 2016-07-19 18:38:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233725980  

On my current system with size = 100000000 I get before your patch:  
  
```  
mpi  
0 resize: 0.310892, send: 0.128063, recv: 0  
1 resize: 0.310591, send: 0, recv: 0.1284  
boost::mpi  
0 resize: 0.048866, send: 1.33238, recv: 0  
1 resize: 0, send: 0, recv: 1.53414  
```  
  
and after  
  
```  
mpi  
0 resize: 0.315995, send: 0.12498, recv: 0  
1 resize: 0.310929, send: 0, recv: 0.130074  
boost::mpi  
0 resize: 0.05023, send: 0.177622, recv: 0  
1 resize: 0, send: 0, recv: 0.227896  
```  
  
which is indeed much better. For size = 1000000 new version gives:  
  
```  
mpi  
0 resize: 0.003075, send: 0.001102, recv: 0  
1 resize: 0.003088, send: 0, recv: 0.001129  
boost::mpi  
0 resize: 0.000648, send: 0.002483, recv: 0  
1 resize: 0, send: 0, recv: 0.003167  
```  
  
while with size = 100000  
  
```  
mpi  
0 resize: 0.000321, send: 0.000226, recv: 0  
1 resize: 0.000315, send: 0, recv: 0.000223  
boost::mpi  
0 resize: 0.000192, send: 0.000225, recv: 0  
1 resize: 0, send: 0, recv: 0.000439  
```  
  
with several tries. I made a new test program (test2.txt -> cpp) which gives with your patch:  
  
```  
mpi  
0 resize: 0.023245, send: 0.027393, recv: 0  
1 resize: 0.023892, send: 0, recv: 0.026774  
boost::mpi  
0 resize: 0.007784, send: 0.341934, recv: 0  
1 resize: 0, send: 0, recv: 0.466891  
```

---

## Comment 10

> Username: nasailja  
> Created_at: 2016-07-19 18:40:27 UTC  
> Updated_at: 2016-07-19 18:41:12 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233726815  

Couldn't upload even with .txt extension (and couldn't add as a comment without closing the pull request) so here it is:  
  
```  
#include "mpi.h"  
#include "chrono"  
#include "boost/mpi/environment.hpp"  
#include "boost/mpi/communicator.hpp"  
#include "boost/serialization/vector.hpp"  
#include "iostream"  
#include "vector"  
#include "thread"  
#include "tuple"  
  
  
// Slightly modified version of  
// https://github.com/Sydius/serialize-tuple/blob/master/serialize_tuple.h  
/*  
Copyright 2011 Christopher Allen Ogden. All rights reserved.  
Redistribution and use in source and binary forms, with or without modification, are  
permitted provided that the following conditions are met:  
   1. Redistributions of source code must retain the above copyright notice, this list of  
      conditions and the following disclaimer.  
   2. Redistributions in binary form must reproduce the above copyright notice, this list  
      of conditions and the following disclaimer in the documentation and/or other materials  
      provided with the distribution.  
THIS SOFTWARE IS PROVIDED BY CHRISTOPHER ALLEN OGDEN ``AS IS'' AND ANY EXPRESS OR IMPLIED  
WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND  
FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL CHRISTOPHER ALLEN OGDEN OR  
CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR  
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR  
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON  
ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING  
NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF  
ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.  
The views and conclusions contained in the software and documentation are those of the  
authors and should not be interpreted as representing official policies, either expressed  
or implied, of Christopher Allen Ogden.  
*/  
namespace boost {  
namespace serialization {  
  
template<unsigned int N>  
struct Serialize  
{  
    template<class Archive, typename... Args>  
    static void serialize(Archive & ar, std::tuple<Args...> & t, const unsigned int version)  
    {  
        ar & std::get<N-1>(t);  
        Serialize<N-1>::serialize(ar, t, version);  
    }  
};  
  
template<>  
struct Serialize<0>  
{  
    template<class Archive, typename... Args>  
    static void serialize(Archive &, std::tuple<Args...> &, const unsigned int) {}  
};  
  
template<class Archive, typename... Args>  
void serialize(Archive & ar, std::tuple<Args...> & t, const unsigned int version)  
{  
    Serialize<sizeof...(Args)>::serialize(ar, t, version);  
}  
  
}} // namespaces  
  
  
using namespace std;  
using namespace std::chrono;  
  
using type_in_vector = tuple<array<int, 3>, array<double, 5>, char>;  
  
  
tuple<void*, int, MPI_Datatype> get_mpi_datatype2(type_in_vector& t)  
{  
    const array<int, 3> block_lengths{  
        get<0>(t).size(),  
        get<1>(t).size(),  
        1  
    };  
    const array<MPI_Aint, 3> displacements{  
        reinterpret_cast<char*>(get<0>(t).data()) - reinterpret_cast<char*>(&t),  
        reinterpret_cast<char*>(get<1>(t).data()) - reinterpret_cast<char*>(&t),  
        reinterpret_cast<char*>(&get<2>(t)) - reinterpret_cast<char*>(&t)  
    };  
    const array<MPI_Datatype, 3> datatypes{  
        MPI_INT,  
        MPI_DOUBLE,  
        MPI_CHAR  
    };  
  
    MPI_Datatype final_type;  
    if (  
        MPI_Type_create_struct(  
            int(block_lengths.size()),  
            block_lengths.data(),  
            displacements.data(),  
            datatypes.data(),  
            &final_type  
        ) != MPI_SUCCESS  
    ) {  
        abort();  
    }  
  
    return make_tuple((void*)&t, 1, final_type);  
}  
  
  
tuple<void*, int, MPI_Datatype> get_mpi_datatype2(vector<type_in_vector>& v)  
{  
    if (v.size() == 0) {  
        abort();  
    }  
  
    MPI_Datatype inner_type;  
    tie(ignore, ignore, inner_type) = get_mpi_datatype2(v[0]);  
  
    MPI_Datatype final_type;  
    if (  
        MPI_Type_create_resized(  
            inner_type,  
            0,  
            sizeof(type_in_vector),  
            &final_type  
        ) != MPI_SUCCESS  
    ) {  
        abort();  
    }  
    MPI_Type_free(&inner_type);  
  
    return make_tuple((void*)v.data(), v.size(), final_type);  
}  
  
int main(int argc, char* argv[]) {  
    boost::mpi::environment env(argc, argv);  
    boost::mpi::communicator world;  
  
    double  
        resize = 0,  
        send = 0,  
        recv = 0;  
  
    vector<type_in_vector> data, empty;  
  
    uint64_t size = 0;  
    if (world.rank() == 0) {  
        cout << "mpi" << endl;  
        size = 1000000;  
        auto temp = high_resolution_clock::now();  
        MPI_Send(&size, 1, MPI_UINT64_T, 1, 0, MPI_COMM_WORLD);  
        send += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
  
        temp = high_resolution_clock::now();  
        data.resize(size);  
        resize += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
        get<0>(data[0])[0] = 1;  
        get<0>(data[0])[1] = 2;  
        get<0>(data[0])[2] = 3;  
        get<2>(data[0]) = 'a';  
        get<0>(data[size - 1])[0] = 123;  
        get<0>(data[size - 1])[1] = 124;  
        get<0>(data[size - 1])[2] = 125;  
        get<2>(data[size - 1]) = 'b';  
  
        temp = high_resolution_clock::now();  
        auto info = get_mpi_datatype2(data);  
        MPI_Type_commit(&get<2>(info));  
        MPI_Send(get<0>(info), get<1>(info), get<2>(info), 1, 0, MPI_COMM_WORLD);  
        MPI_Type_free(&get<2>(info));  
        send += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
  
    } else {  
        auto temp = high_resolution_clock::now();  
        MPI_Recv(&size, 1, MPI_UINT64_T, 0, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);  
        recv += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
  
        temp = high_resolution_clock::now();  
        data.resize(size);  
        resize += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
  
        temp = high_resolution_clock::now();  
        auto info = get_mpi_datatype2(data);  
        MPI_Type_commit(&get<2>(info));  
        MPI_Recv(get<0>(info), get<1>(info), get<2>(info), 0, 0, MPI_COMM_WORLD, MPI_STATUS_IGNORE);  
        MPI_Type_free(&get<2>(info));  
        recv += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
        if (get<0>(data[0])[0] != 1) abort();  
        if (get<0>(data[0])[1] != 2) abort();  
        if (get<0>(data[0])[2] != 3) abort();  
        if (get<2>(data[0]) != 'a') abort();  
        if (get<0>(data[size - 1])[0] != 123) abort();  
        if (get<0>(data[size - 1])[1] != 124) abort();  
        if (get<0>(data[size - 1])[2] != 125) abort();  
        if (get<2>(data[size - 1]) != 'b') abort();  
    }  
    cout << world.rank() << " resize: " << resize << ", send: " << send << ", recv: " << recv << std::endl;  
    resize = send = recv = 0;  
    data = empty;  
  
    this_thread::sleep_for(seconds(1));  
    world.barrier();  
    this_thread::sleep_for(seconds(1));  
  
    if (world.rank() == 0) {  
        cout << "boost::mpi" << endl;  
        size = 1000000;  
        auto temp = high_resolution_clock::now();  
        data.resize(size);  
        resize += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
        get<0>(data[0])[0] = 1;  
        get<0>(data[0])[1] = 2;  
        get<0>(data[0])[2] = 3;  
        get<2>(data[0]) = 'a';  
        get<0>(data[size - 1])[0] = 123;  
        get<0>(data[size - 1])[1] = 124;  
        get<0>(data[size - 1])[2] = 125;  
        get<2>(data[size - 1]) = 'b';  
  
        temp = high_resolution_clock::now();  
        world.send(1, 0, data);  
        send += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
  
    } else {  
        auto temp = high_resolution_clock::now();  
        world.recv(0, 0, data);  
        recv += duration_cast<duration<double>>(high_resolution_clock::now() - temp).count();  
        if (get<0>(data[0])[0] != 1) abort();  
        if (get<0>(data[0])[1] != 2) abort();  
        if (get<0>(data[0])[2] != 3) abort();  
        if (get<2>(data[0]) != 'a') abort();  
        if (get<0>(data[size - 1])[0] != 123) abort();  
        if (get<0>(data[size - 1])[1] != 124) abort();  
        if (get<0>(data[size - 1])[2] != 125) abort();  
        if (get<2>(data[size - 1]) != 'b') abort();  
    }  
    cout << world.rank() << " resize: " << resize << ", send: " << send << ", recv: " << recv << std::endl;  
}  
```

---

## Comment 11

> Username: Belcourt  
> Created_at: 2016-07-19 18:41:44 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233727166  

Okay, good.  Well I need to clean up the patch, test it a bit more for both pod and udt types.  Will try to get into develop here shortly.  Thanks for testing the patch.  I see you added the source, I'll look at that in a minute.

---

## Comment 12

> Username: nasailja  
> Created_at: 2016-07-19 18:45:38 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233728260  

Thanks, by the way I used this for testing: boost-1.60.0, open-mpi-1.10.2, g++-mp-5 (MacPorts gcc5 5.3.0_1) 5.3.0.

---

## Comment 13

> Username: Belcourt  
> Created_at: 2016-07-19 18:49:02 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233729178  

Sure.  So the new test case the recv is about factor of 20 slower with Boost MPI.  I suspect we can significantly improve that as well.

---

## Comment 14

> Username: matthiastroyer  
> Created_at: 2016-07-19 19:40:47 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233742986  

Have you checked performance using skeleton&content. This is the mechanism that Boost.MPI uses for fast transfer of datatsructures containing PODs. If you just send a vector, the content gets serialized because the receiving side may not have the same size. If you know that the receiving side has the same size, then just send the contents and you should see faster performance.

---

## Comment 15

> Username: nasailja  
> Created_at: 2016-07-19 20:42:02 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233759068  

I haven't tried skeletons but in my use case the size of the vector changes constantly so skeletons wouldn't be faster I think. But it would make the boost part of the test program resemble the MPI part more so I might try it there.

---

## Comment 16

> Username: Belcourt  
> Created_at: 2016-07-20 00:22:34 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233805187  

test2.cpp udt vector with size = 1e8 items:  
  
mpi  
0 resize: 2.55685, send: 1.86444, recv: 0  
1 resize: 2.5334, send: 0, recv: 1.88791  
boost::mpi  
0 resize: 0.881014, send: 2.60478, recv: 0  
1 resize: 0, send: 0, recv: 3.48585  
  
Accomplished this with changes only to your test2.cpp.  Will send the mods back to you.

---

## Comment 17

> Username: matthiastroyer  
> Created_at: 2016-07-20 02:17:01 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233820678  

Why should it not be faster? Sending the skeleton of a vector simply sends the size, and then sending the contents uses MPI Datatypes to send the vector without copying.

---

## Comment 18

> Username: nasailja  
> Created_at: 2016-07-20 13:36:14 UTC  
> Updated_at: 2016-07-20 13:36:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233950739  

> Why should it not be faster?  
  
Because I'd have to update the skeleton after each send which is what communicator.txt above is doing in a regular send.

---

## Comment 19

> Username: nasailja  
> Created_at: 2016-07-20 14:07:52 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233959944  

> mpi  
> 0 resize: 2.55685, send: 1.86444, recv: 0  
> 1 resize: 2.5334, send: 0, recv: 1.88791  
> boost::mpi  
> 0 resize: 0.881014, send: 2.60478, recv: 0  
> 1 resize: 0, send: 0, recv: 3.48585  
  
This is pretty good but it would be nice if users didn't have to write their own boost::mpi::get_mpi_datatype functions for (recursive) STL containers of basic types.  
  
Hmm maybe the largest problem is that when users do have to write their own functions they can't take advantage of existing ones because the API doesn't expose the address assumed in the datatype. For example if I have a class that contains another one that already has a get_mpi_datatype I can't use it because in order to do that I'd have to know the address relative to which get_mpi_datatype returns the type so that I can give the type and the address to MPI_Type_create_struct. Or can this be solved without returning the address (and count while we're at it) from boost::mpi::get_mpi_datatype?

---

## Comment 20

> Username: Belcourt  
> Created_at: 2016-07-20 14:36:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233968650  

It's about the same performance:  
  
mpi  
0 resize: 2.55685, send: 1.86444, recv: 0  
1 resize: 2.5334, send: 0, recv: 1.88791  
boost::mpi  
0 resize: 0.881014, send: 2.60478, recv: 0  
1 resize: 0, send: 0, recv: 3.48585  
  
The total time is the sum of resize + recv on rank 1, so for mpi, sum = 4.42 whereas for boost::mpi, the recv includes both the time to resize the vector and the actual communication cost.  In this case, boost::mpi is a bit faster.

---

## Comment 21

> Username: nasailja  
> Created_at: 2016-07-20 14:45:57 UTC  
> Updated_at: 2016-07-20 15:06:54 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233971644  

(edited because I misread the above comment) I'm getting this error from your version of test2.cpp (with 0.1*size):  
  
```  
mpi  
0 resize: 0.237638, send: 0.214414, recv: 0  
1 resize: 0.239999, send: 0, recv: 0.212078  
boost::mpi  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::mpi::exception> >'  
  what():  MPI_Send: MPI_ERR_TYPE: invalid datatype  
[Jeffs-MacBook-Pro-3:00595] *** Process received signal ***  
[Jeffs-MacBook-Pro-3:00595] Signal: Abort trap: 6 (6)  
[Jeffs-MacBook-Pro-3:00595] Signal code:  (0)  
[Jeffs-MacBook-Pro-3:00595] *** End of error message ***  
```

---

## Comment 22

> Username: Belcourt  
> Created_at: 2016-07-20 14:49:18 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233972740  

Right, that error is because you've failed to commit the new MPI_Datatype inside the get_mpi_datatype function.  Realize the boost::mpi calls your get_mpi_datatype function so that must be committed before you return from the function, as I did in the code I sent you.  
  
The problem is in here:  
  
template <>  
MPI_Datatype  
boost::mpi::get_mpi_datatype<type_in_vector>(const type_in_vector& t)  
  
or because you left a call to MPI_Type_free in there somewhere.

---

## Comment 23

> Username: nasailja  
> Created_at: 2016-07-20 15:02:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233977190  

Ah ok, that's a bit premature I think because types only have to be committed before sending and not e.g. when the result of get_mpi_datatype is used to create new types (in boost or elsewhere). So if boost is doing the sending it would make sense for boost to commit the datatype.

---

## Comment 24

> Username: Belcourt  
> Created_at: 2016-07-20 15:21:00 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233983064  

This boost::mpi behavior is an implementation detail and can't be changed.  
  
That's why Matthias said to use the skeleton + content as that's the supported / intended way to accomplish what you're trying to do.  I added the support for std::vector in the point to point send and recv for vector<pod> to get the performance users expect for vector<pod> types.  
  
As for the vector<udt> in your second test case, I was trying to show you other ways to achieve the same basic performance for vectors of user defined types as you can now get for vector<pod>, by reusing the changes I made for vector<pod>.  This is a shortcut that may not ultimately work for you, and you may find the skeleton / content approach superior.

---

## Comment 25

> Username: Belcourt  
> Created_at: 2016-07-20 15:22:35 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233983574  

I hate github, it totally mangles and removes text I wrote in the response above, making my above response largely unintelligible.  I'll email you separately.

---

## Comment 26

> Username: nasailja  
> Created_at: 2016-07-20 15:39:07 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233988887  

> This boost::mpi behavior is an implementation detail and can't be changed.  
> That's why Matthias said to use the skeleton + content...  
  
Ok so in my use case where vector sizes change most of the time and unpredictably I'd have to always do a skeleton+content for every transfer of data, making it pretty much the same as using MPI directly, without maybe having to manually resize the receiving container, correct?

---

## Comment 27

> Username: nasailja  
> Created_at: 2016-07-20 15:43:38 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233990319  

> totally mangles and removes text I wrote  
  
If you include your text in a code section it won't be altered at all I think, surround the text with three back tics on both sides: `!"#€%&/()=?©@£$∞§|[]`

---

## Comment 28

> Username: Belcourt  
> Created_at: 2016-07-20 15:53:50 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233993480  

Ah, thanks for that.  I'll try that next time.

---

## Comment 29

> Username: nasailja  
> Created_at: 2016-07-20 15:54:56 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233993864  

> This boost::mpi behavior is an implementation detail and can't be changed.  
  
Is it really an implementation detail by the way? Your version of my test changed get_mpi_datatype2 to boost::mpi::get_datatype but boost documentation doesn't mention specializing boost::mpi::get_datatype for custom types so is that not the recommended way to send custom types with boost::mpi? If it's not how would the recommended version of my test look like and if it is then whoever implements boost::mpi::get_datatype should know that the type has to be committed.

---

## Comment 30

> Username: Belcourt  
> Created_at: 2016-07-20 16:12:18 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-233998999  

```Yes, it's an implementation detail because it's not mentioned (documented) and that's why it's not officially supported, but I prefer using this approach.  
  
Note that if you use this approach (defining your own specialization of get_mpi_datatype), you must commit the MPI_Datatype before the get_mpi_datatype function returns to boost::mpi.  We can't push that requirement into boost::mpi that boost::mpi call commit everytime it calls get_mpi_datatype.  
  
I think the recommended approach is skeleton & content.  
  
```  
```

---

## Comment 31

> Username: Belcourt  
> Created_at: 2016-07-20 16:24:04 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-234002249  

'''  
Here's how I changed your code:  
  
namespace {  
  MPI_Datatype type_in_vector_MPI_Datatype;  
}  
  
template <>  
struct boost::mpi::is_mpi_datatype<type_in_vector> : mpl::true_ {  
};  
  
template <>  
MPI_Datatype  
boost::mpi::get_mpi_datatype<type_in_vector>(const type_in_vector& t)  
{  
  return type_in_vector_MPI_Datatype;  
}  
  
And then in main or somewhere else you create and commit the type_in_vector_MPI_Datatype.  That way the get_mpi_datatype call just returns a type that was allocated and committed somewhere else in the code.  
'''

---

## Comment 32

> Username: matthiastroyer  
> Created_at: 2016-07-20 16:32:00 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-234004557  

Here is one question I have: why do you need to overload get_mpi_datatype? Once you specialize is_mpi_datatype and provide a serialize function for tour datatype Boost.MPI should be able to build that datatype.  
  
Also, I see no problem in making get_mpi_datatype part of the public interface of Boost.MPI if there is a good reason for it and the above approach somehow does not work for your type.

---

## Comment 33

> Username: Belcourt  
> Created_at: 2016-07-20 17:35:48 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-234022719  

Ilja's test code has serialization functions but they're slower than specializing get_mpi_datatype combined with the mod's to support std::vector in the boost::mpi send and recv api.  
  
Results using vector send and recv with specialized get_mpi_datatype:  
boost::mpi  
0 resize: 0.213217, send: 0.385228, recv: 0  
1 resize: 0, send: 0, recv: 0.598461  
  
Results using serialization functions vector send and recv:  
boost::mpi  
0 resize: 0.210793, send: 0.427438, recv: 0  
1 resize: 0, send: 0, recv: 0.638267  
  
Results using serialization without vector send and recv:  
boost::mpi  
0 resize: 0.210196, send: 0.828384, recv: 0  
1 resize: 0, send: 0, recv: 1.47116  
  
So about 10% faster using vector api with specialized get_mpi_datatype.  Haven't tested skeleton & content.

---

## Comment 34

> Username: nasailja  
> Created_at: 2016-07-20 19:12:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-234051381  

I'm working on another test case but can't compile skeleton+content on vector of vectors, when trying to:  
  
```  
vector<vector<double>> data2(nr_vectors);  
...  
world.send(1, 0, boost::mpi::skeleton(data2));  
auto content = boost::mpi::get_content(data2);  
world.send(1, 0, content);  
```  
  
I get:  
  
```  
test3.cpp:140:46: error: invalid use of incomplete type 'const struct boost::mpi::skeleton_proxy<std::vector<std::vector<double> > >'  
   world.send(1, 0, boost::mpi::skeleton(data2));  
                                              ^  
In file included from .../boost/mpi/communicator.hpp:31:0,  
                 from test3.cpp:4:  
.../boost/mpi/skeleton_and_content_fwd.hpp:22:27: note: declaration of 'struct boost::mpi::skeleton_proxy<std::vector<std::vector<double> > >'  
 template <class T> struct skeleton_proxy;  
                           ^  
test3.cpp:141:47: error: invalid use of incomplete type 'const class boost::mpi::content'  
   auto content = boost::mpi::get_content(data2);  
                                               ^  
In file included from .../boost/mpi/communicator.hpp:31:0,  
                 from test3.cpp:4:  
.../boost/mpi/skeleton_and_content_fwd.hpp:24:7: note: forward declaration of 'class boost::mpi::content'  
 class content;  
       ^  
```  
  
Is `vector<vector<double>>` supported? If not would it be easy to add? Thanks

---

## Comment 35

> Username: Belcourt  
> Created_at: 2016-07-21 20:27:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-234373619  

> Is vector<vector<double>> supported?  
  
If it fails to compile, I'd guess not.  
  
> If not would it be easy to add?  
  
No idea, you could submit an issue requesting this.  Or ideally a PR with a patch.

---

## Comment 36

> Username: nasailja  
> Created_at: 2016-07-22 13:28:34 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-234543635  

> Or ideally a PR with a patch.  
  
How about a function returning address, count and datatype instead? It would also solve the problem not being able to use boost::mpi::get_datatype to make new datatypes which consist of types already supported by boost.

---

## Comment 37

> Username: Belcourt  
> Created_at: 2016-08-06 01:34:53 UTC  
> Url: https://github.com/boostorg/mpi/pull/32#issuecomment-237998857  

Several workarounds have been suggested, closing.

---
