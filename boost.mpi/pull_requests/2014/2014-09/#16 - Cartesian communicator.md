# #16 Cartesian communicator [Merged]

> Username: aminiussi  
> Created at: 2014-09-15 15:30:17 UTC  
> Updated at: 2016-10-28 12:58:37 UTC  
> Merged at: 2016-10-19 12:45:58 UTC  
> Closed at: 2016-10-19 12:45:58 UTC  
> Url: https://github.com/boostorg/mpi/pull/16  

This is a tentative implementation of cartesian communicator with associated documentation and test.  
As far as the documentation is concerned, I just added the C mapping and the doxygen.  
  
There are certainly a few issues. On point for which I am not sure about what to do is the topology description which in the C API id usually represented through 2 array (one for the dimensions, one for the periodicity). I choosed to represent it as a sequence of itermediate object (cartesian_dimension) representing a pair<int,bool> {dimension, periodic). The idea beeing that, using C++11ish syntax, it would give something like:  
cartesian_communicator( { {2, true}, {3, false}} );  
Note that the class does not use C++11 features (at least I don;t think so).  
  
I choose not to templatize the class on the grid dimension. Altough I suspect most real world use cases could benefit from it, I wasn't comfortable wit hthhe lost of runtime flexibility. Also, such  a templatized class would benefit from C++11 constructs. I guess it could be added later (maybe as a sub class), but if we plan to go there, with should think out the class names now. (one could be named cartesian_communicator vs grid_communicator ?).

---

## Comment 1

> Username: rhl-  
> Created_at: 2016-08-21 18:44:49 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-241274196  

Is there any progress on getting a review for this? I would love to have this in boost mainline.

---

## Comment 2

> Username: aminiussi  
> Created_at: 2016-08-24 07:23:30 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-241978956  

On 21/08/2016 20:44, Ryan H. Lewis wrote:  
  
> Is there any progress on getting a review for this? I would love to   
> have this in boost mainline.  
>   
> No progress so far. I guess that if you want to have a look at it...  
> Just a few points:  
> a) Boost build and test system has now become such a PITA that it will   
> probably take me some time to get back to it (provided it even work on   
> my system). Not to mention the documentation system that I could never   
> get up and running (it is an issue since such a feature will require   
> some documentation).  
> b) I can mostly test on my working platform (Linux/Intel compilers and MPI)  
> c) There is a pending design issue: Right now the boost layer is quite   
> dynamic (as the underlying C implementation) in the way it handle the   
> comunicator size and dimensions . I am not convinced that's the best   
> choice. It is hard for me to imagine a use case where such a flexibility   
> is required and maybe a more templatized implementation could ensure   
> some compile time checking when spiting communicators. Any input on that   
> one...  
  
I'll try to get back to a) shortly, hoping my conclusion won't be to   
fork outside of Boost.  
  
Cheers  
  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/mpi/pull/16#issuecomment-241274196, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AAhsjLiapt1rN33b5OWXwSjGQFfWoWmMks5qiJyhgaJpZM4CiGdJ.

---

## Comment 3

> Username: timo-oster  
> Created_at: 2016-10-13 14:53:27 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-253537071  

Hi,  
  
I've just spent two days writing my own version of a cartesian_communicator for boost and wanted to check how I would go about contributing it, only to find out somebody else has already beat me to the job ;)  
  
Your description of the problems you had in trying to set up the test system has kind of discouraged me, but perhaps I can at least contribute something to the discussion here, based on the difference between our implementations:  
1. Your choice of writing a separate class for the cartesian dimensions is interesting and certainly expressive. I chose to go a somewhat more flexible way by defining the constructor as a template that accepts two `SinglePassRange`s:  
     
   ``` .cpp  
   template<typename Shape, typename Periods>  
     explicit  
     cartesian_communicator(const communicator& comm, const Shape& shape,  
                              const Periods& periods, bool reorder = false);  
   ```  
     
   This has the drawback of requiring compile-time and run-time checks if the two supplied arguments are the correct type and size, etc. but you can pass in any kind of container that you want as long as they are iterable and the elements convert to `int`, resp. `bool`. I'm not sure yet if I prefer my version or yours, which is safer but might require more code on the client side to set up the topology. The brace-initialize syntax is of course a big help here, but only works when you know the number of dimensions at compile time.  
2. Your interface to the communicator is quite close to the underlying MPI calls. In some cases, I find this unnecessarily restrictive. For example, many of your member functions such as `std::vector<int>& coords(int rk, std::vector<int>& cbuf) const` require the user to create a vector to hold the results themselves before calling the function. In my implementation, all those functions allocate the storage themselves and return the container. E.g.   
     
   ``` .cpp  
   std::vector<int> coordinates(int rank) const;  
   ```  
     
   I doubt that the overhead incurred by this is significant, and none of the other communicator's functions require the user to allocate storage and pass it via an argument like this.  
3. You require the user to pass in coordinates as `std::vector<int>`. I chose to go a little more flexible route and made the argument a template again, checking that it models `SinglePassRange` and it's elements are convertible to `int`:  
     
   ``` .cpp  
   template<typename Coordinates>  
   int cartesian_communicator::rank(const Coordinates& coords) const  
   {  
     BOOST_CONCEPT_ASSERT((SinglePassRangeConcept<Coordinates>));  
     using coords_value_type = typename std::iterator_traits<  
                                 typename boost::range_iterator<Coordinates>::type  
                              >::value_type;  
     static_assert(std::is_convertible<coords_value_type, int>::value,  
                   "elements of coords must be convertible to int");  
   #ifndef BOOST_DISABLE_ASSERTS  
     BOOST_ASSERT(coords.size() == this->num_dimensions());  
   #endif  
     
     std::vector<int> c(boost::begin(coords), boost::end(coords));  
     int result_rank;  
     BOOST_MPI_CHECK_RESULT(MPI_Cart_rank,  
                            ((MPI_Comm)*(this->comm_ptr),  
                             &c[0],  
                             &result_rank));  
     return result_rank;  
   }  
   ```  
     
   Like with the constructor, this potentially requires a little less code on the client side if it is not using `std::vector<int>` already for representing coordinates, but the template code is less readable than yours because of all the concept checks and template metafunctions.  
     
   As an aside, I am also using `std::vector<int>` as a return type for coordinates, but I find it sub-optimal. It would be ideal to have a type for representing coordinates in the cartesian grid that supports element-wise addition, so that you could simply add an offset vector to an existing coordinate to obtain a new one like so:  
     
   ``` .cpp  
     // assuming we are working with a 3-dimensional cartesian topology  
     // get coordinates of current process:  
     coords_t coords = comm.coordinates(comm.rank());  
     // get the rank of the process that is one up in x direction   
     // and one down in y direction:  
     int neighbor_rank = comm.rank(coords + coords_t{1, -1, 0});  
   ```  
     
   Unfortunately, `std::valarray`, which supports this, is not compatible with the `SinglePassRangeConcept`.  
4. I tried to model my implementation on the existing `graph_communicator`, which provides iterators for the neighbors of a graph node. In this spirit, I implemented two different iterators for processes in the cartesian topology: `direct_neighbor_iterator` and `neighbor_iterator`. The first iterates over the neighboring processes that differ only in one coordinate direction ([the von Neumann Neighborhood](https://en.wikipedia.org/wiki/Von_Neumann_neighborhood)), the second iterates over all neighbors that differ by a maximum of 1 in any coordinate direction ([Moore Neighborhood](https://en.wikipedia.org/wiki/Moore_neighborhood)). They yield pairs of the rank of the neighbor and it's coordinates. You can use them like so:  
     
   ``` .cpp  
   for(const auto& ir: mpi::neighbors(comm.rank(), comm))  
   {  
       std::cout << "rank: " << ir.first  << std::endl;  
       std::cout << " coords: ";  
       for(auto i: ir.second)  
       {  
           std::cout << i << " ";  
       }  
       std::cout << std::endl;  
   }  
   ```  
     
   This is useful if you have to communicate with all neighbors. You can iterate them, determine in which direction the neighbor is by investigating the coordinates (again, a type that supports vector subtraction would come in handy here), and issue the communication requests in the loop. If you just have to communicate in one specific direction, your `shifted_ranks` is more useful. I haven't implemented that yet on my side.  
  
This has been a giant wall of text. Sorry for that. I hope I could give a different perspective on the problem. Maybe we can combine our efforts and come up with a combination of both our approaches that works better than each one separately. I've uploaded my current implementation as a gist [here](https://gist.github.com/timo-oster/d367a8bfdb18e971ab5e6030362d14da). It is still missing some pieces (such as the conversion of a `communicator` to a `cartesian_communicator`), and there is no test code, but the bulk of what I wanted the class to do is there.

---

## Comment 4

> Username: Belcourt  
> Created_at: 2016-10-19 14:28:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-254829555  

What was the resolution of these two cartesian implementations?  I've been busy but was planning on looking at this for the next release.

---

## Comment 5

> Username: aminiussi  
> Created_at: 2016-10-19 15:13:22 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-254843539  

After discussion, I'm merging some suggestions into the implementation   
currently in develop (I must confess it was mushed after un policy   
change in my git version). There are two issues in serialization (pull   
request 46&47) which are preventing some test to pass. I am waiting for   
thos issues to be fixed on master before merging the current implementation.  
That implementation is mostly a subset of the two implementations which   
can be completed in the future while preserving backward compatibility.  
  
I just don't want to merge on master until that serialization issue is   
fixed.  
  
On 19/10/2016 16:28, Noel Belcourt wrote:  
  
> What was the resolution of these two cartesian implementations? I've   
> been busy but was planning on looking at this for the next release.  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/mpi/pull/16#issuecomment-254829555, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AAhsjGcqnBWTvnqMvqevKtpn4Tod4fs4ks5q1ikGgaJpZM4CiGdJ.

---

## Comment 6

> Username: aminiussi  
> Created_at: 2016-10-19 15:16:45 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-254844615  

Note that the develop version is tested on CentOS 6.5 with intel   
compiler 15.0.3 (the underlying stl header are those of g++4.4.7) and   
Intel MPI. With and without C++11 compilation flag.  
  
It would be nice if others could test other configurations.  
  
Regards  
  
On 19/10/2016 16:28, Noel Belcourt wrote:  
  
> What was the resolution of these two cartesian implementations? I've   
> been busy but was planning on looking at this for the next release.  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/mpi/pull/16#issuecomment-254829555, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AAhsjGcqnBWTvnqMvqevKtpn4Tod4fs4ks5q1ikGgaJpZM4CiGdJ.  
  
##   
  
Alain Miniussi  
Pole Génie Logiciel Scientifique  
Observatoire de la Côte d'Azur  
Blv de l'Observatoire, Nice

---

## Comment 7

> Username: Belcourt  
> Created_at: 2016-10-19 17:45:11 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-254887719  

So the Sandia Boost nightly testing runs develop MPI tests on Darwin and Linux, with Clang, Gcc, Intel (mostly using OpenMPI) so there's some additional coverage there.  
  
I haven't looked yet but are there at least one (or a few) Cartesian communicator tests checked into develop and running?

---

## Comment 8

> Username: aminiussi  
> Created_at: 2016-10-19 20:50:04 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-254936707  

There are two of them, they're mostly limited to the new features   
introduced by the acrtesian communicator (topology, shifted cooord..)  
  
On 19/10/2016 19:45, Noel Belcourt wrote:  
  
> So the Sandia Boost nightly testing runs develop MPI tests on Darwin   
> and Linux, with Clang, Gcc, Intel (mostly using OpenMPI) so there's   
> some additional coverage there.  
>   
> I haven't looked yet but are there at least one (or a few) Cartesian   
> communicator tests checked into develop and running?  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/mpi/pull/16#issuecomment-254887719, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AAhsjDQVTu4m05TvAQB7hhpUdECLo_TAks5q1lcngaJpZM4CiGdJ.  
  
##   
  
Alain Miniussi  
Pole Génie Logiciel Scientifique  
Observatoire de la Côte d'Azur  
Blv de l'Observatoire, Nice

---

## Comment 9

> Username: aminiussi  
> Created_at: 2016-10-20 06:44:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-255022584  

The failure I see on nightly matches the Boost.Serialization issues for   
which I submitted the pull request  
https://github.com/boostorg/serialization/pull/47  
https://github.com/boostorg/serialization/pull/46  
  
On 19/10/2016 22:49, alainm wrote:  
  
> There are two of them, they're mostly limited to the new features   
> introduced by the acrtesian communicator (topology, shifted cooord..)  
>   
> On 19/10/2016 19:45, Noel Belcourt wrote:  
>   
> > So the Sandia Boost nightly testing runs develop MPI tests on Darwin   
> > and Linux, with Clang, Gcc, Intel (mostly using OpenMPI) so there's   
> > some additional coverage there.  
> >   
> > I haven't looked yet but are there at least one (or a few) Cartesian   
> > communicator tests checked into develop and running?  
> >   
> > —  
> > You are receiving this because you modified the open/close state.  
> > Reply to this email directly, view it on GitHub   
> > https://github.com/boostorg/mpi/pull/16#issuecomment-254887719, or   
> > mute the thread   
> > https://github.com/notifications/unsubscribe-auth/AAhsjDQVTu4m05TvAQB7hhpUdECLo_TAks5q1lcngaJpZM4CiGdJ.  
>   
> ##   
>   
> Alain Miniussi  
> Pole Génie Logiciel Scientifique  
> Observatoire de la Côte d'Azur  
> Blv de l'Observatoire, Nice  
  
##   
  
Alain Miniussi  
Pole Génie Logiciel Scientifique  
Observatoire de la Côte d'Azur  
Blv de l'Observatoire, Nice

---

## Comment 10

> Username: aminiussi  
> Created_at: 2016-10-28 12:37:51 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-256909690  

It appears that 1.63 is closed and I couldn't get any new regarding the   
serialize fixes I needed:  
https://github.com/boostorg/serialization/pull/46  
https://github.com/boostorg/serialization/pull/47  
  
so I'm afraid it will have to wait for the next one.  
  
The cartesian communicator is on develop (along with the global   
communication optimization related with non primitive types) .  
  
I already asked twice on the list but got not answer so far.  
  
On 19/10/2016 19:45, Noel Belcourt wrote:  
  
> So the Sandia Boost nightly testing runs develop MPI tests on Darwin   
> and Linux, with Clang, Gcc, Intel (mostly using OpenMPI) so there's   
> some additional coverage there.  
>   
> I haven't looked yet but are there at least one (or a few) Cartesian   
> communicator tests checked into develop and running?  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/mpi/pull/16#issuecomment-254887719, or   
> mute the thread   
> https://github.com/notifications/unsubscribe-auth/AAhsjDQVTu4m05TvAQB7hhpUdECLo_TAks5q1lcngaJpZM4CiGdJ.  
  
##   
  
Alain Miniussi  
Pole Génie Logiciel Scientifique  
Observatoire de la Côte d'Azur  
Blv de l'Observatoire, Nice

---

## Comment 11

> Username: aminiussi  
> Created_at: 2016-10-28 12:58:36 UTC  
> Url: https://github.com/boostorg/mpi/pull/16#issuecomment-256913768  

Although it's only closed for new libs and breaking changes....  
  
On 28/10/2016 14:37, alainm wrote:  
  
> It appears that 1.63 is closed and I couldn't get any new regarding   
> the serialize fixes I needed:  
> https://github.com/boostorg/serialization/pull/46  
> https://github.com/boostorg/serialization/pull/47  
>   
> so I'm afraid it will have to wait for the next one.  
>   
> The cartesian communicator is on develop (along with the global   
> communication optimization related with non primitive types) .  
>   
> I already asked twice on the list but got not answer so far.  
>   
> On 19/10/2016 19:45, Noel Belcourt wrote:  
>   
> > So the Sandia Boost nightly testing runs develop MPI tests on Darwin   
> > and Linux, with Clang, Gcc, Intel (mostly using OpenMPI) so there's   
> > some additional coverage there.  
> >   
> > I haven't looked yet but are there at least one (or a few) Cartesian   
> > communicator tests checked into develop and running?  
> >   
> > —  
> > You are receiving this because you modified the open/close state.  
> > Reply to this email directly, view it on GitHub   
> > https://github.com/boostorg/mpi/pull/16#issuecomment-254887719, or   
> > mute the thread   
> > https://github.com/notifications/unsubscribe-auth/AAhsjDQVTu4m05TvAQB7hhpUdECLo_TAks5q1lcngaJpZM4CiGdJ.  
>   
> ##   
>   
> Alain Miniussi  
> Pole Génie Logiciel Scientifique  
> Observatoire de la Côte d'Azur  
> Blv de l'Observatoire, Nice  
  
##   
  
Alain Miniussi  
Pole Génie Logiciel Scientifique  
Observatoire de la Côte d'Azur  
Blv de l'Observatoire, Nice

---
