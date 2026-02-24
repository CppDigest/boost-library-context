# #77 - Drop MPI1 usage [Open]

> Username: opoplawski  
> Created at: 2018-11-26 03:24:27 UTC  
> Updated at: 2018-12-10 23:14:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/77  

Compilation of Boost MPI using code fails with OpenMPI 4.0.  For example:  
```  
[  1%] Building CXX object src/core/cluster_analysis/CMakeFiles/cluster_analysis.dir/Cluster.cpp.o  
cd /builddir/build/BUILD/espresso-4.0.0/openmpi_build/src/core/cluster_analysis && /usr/bin/c++  -DH5XX_USE_MPI -Dcluster_analysis_EXPORTS -I/builddir/build/BUILD/espresso-4.0.0/src -I/builddir/build/BUILD/espresso-4.0.0/openmpi_build/src -I/builddir/build/BUILD/espresso-4.0.0/src/core -I/builddir/build/BUILD/espresso-4.0.0/openmpi_build/src/core -isystem /usr/include/openmpi-x86_64  -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -pthread -Wall -Wno-sign-compare -Wno-unused-function -Wno-unused-variable -DNDEBUG -fPIC   -std=c++11 -o CMakeFiles/cluster_analysis.dir/Cluster.cpp.o -c /builddir/build/BUILD/espresso-4.0.0/src/core/cluster_analysis/Cluster.cpp  
BUILDSTDERR: In file included from /usr/include/boost/mpi/communicator.hpp:17:0,  
BUILDSTDERR:                  from /builddir/build/BUILD/espresso-4.0.0/src/core/communication.hpp:56,  
BUILDSTDERR:                  from /builddir/build/BUILD/espresso-4.0.0/src/core/grid.hpp:47,  
BUILDSTDERR:                  from /builddir/build/BUILD/espresso-4.0.0/src/core/cluster_analysis/Cluster.cpp:19:  
BUILDSTDERR: /usr/include/boost/mpi/detail/mpi_datatype_primitive.hpp: In constructor 'boost::mpi::detail::mpi_datatype_primitive::mpi_datatype_primitive(const void*)':  
BUILDSTDERR: /usr/include/boost/mpi/detail/mpi_datatype_primitive.hpp:52:7: error: 'MPI_Address' was not declared in this scope  
BUILDSTDERR:        BOOST_MPI_CHECK_RESULT(MPI_Address,(const_cast<void*>(orig), &origin));  
BUILDSTDERR:        ^  
BUILDSTDERR: /usr/include/boost/mpi/detail/mpi_datatype_primitive.hpp: In member function 'ompi_datatype_t* boost::mpi::detail::mpi_datatype_primitive::get_mpi_datatype()':  
BUILDSTDERR: /usr/include/boost/mpi/detail/mpi_datatype_primitive.hpp:75:9: error: 'MPI_Type_struct' was not declared in this scope  
BUILDSTDERR:          BOOST_MPI_CHECK_RESULT(MPI_Type_struct,  
BUILDSTDERR:          ^  
BUILDSTDERR: /usr/include/boost/mpi/detail/mpi_datatype_primitive.hpp: In member function 'void boost::mpi::detail::mpi_datatype_primitive::save_impl(const void*, MPI_Datatype, int)':  
BUILDSTDERR: /usr/include/boost/mpi/detail/mpi_datatype_primitive.hpp:108:7: error: 'MPI_Address' was not declared in this scope  
BUILDSTDERR:        BOOST_MPI_CHECK_RESULT(MPI_Address,(const_cast<void*>(p), &a));  
BUILDSTDERR:        ^  
BUILDSTDERR: make[2]: *** [src/core/cluster_analysis/CMakeFiles/cluster_analysis.dir/Cluster.cpp.o] Error 1  
```

---

## Comment 1

> Username: aminiussi  
> Created at: 2018-12-09 22:53:56 UTC  
> Updated at: 2018-12-09 22:54:09 UTC  
> Url: https://github.com/boostorg/mpi/issues/77#issuecomment-445579715  

opened pull request #78

---

## Comment 2

> Username: aminiussi  
> Created at: 2018-12-09 23:15:27 UTC  
> Url: https://github.com/boostorg/mpi/issues/77#issuecomment-445581931  

Apart from MPI_Address, did you find any other deprecated usage ?  
  
I just installed openmpi 4.0.0 and it went ok.  
  
Thanks

---

## Comment 3

> Username: aminiussi  
> Created at: 2018-12-09 23:16:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/77#issuecomment-445582309  

merged in develop

---

## Comment 4

> Username: aminiussi  
> Created at: 2018-12-10 23:02:28 UTC  
> Url: https://github.com/boostorg/mpi/issues/77#issuecomment-446007623  

Bug there is a problem: OpenMPI is supposed to set MPI_VERSION to 3 (for 4.0) and MPI_Address is protected according to MPI_VERSION >= 2.  
  
So I'm gonna install openMPI 4.0

---

## Comment 5

> Username: aminiussi  
> Created at: 2018-12-10 23:14:00 UTC  
> Url: https://github.com/boostorg/mpi/issues/77#issuecomment-446010572  

Just past the test with th 4.0 version of OpenMPI. How did you get that error message ?   
  
It seems you're using OpenMPI 4.0, was is modified ?  
  
Thanks
