# #96 - Failed to compile a program with Boost 1.78.0: 'library_version' was not declared [Closed]

> Username: rangsimanketkaew  
> Created at: 2022-01-10 23:00:13 UTC  
> Updated at: 2023-11-15 10:05:51 UTC  
> Closed at: 2023-11-15 10:05:51 UTC  
> Url: https://github.com/boostorg/property_tree/issues/96  

I have been trying to compile a program package called *Bagel* (https://nubakery.org/quickstart/installation_guide.html) requiring boost as a dependency. Unfortunately, I ran into the following error when making bagel:  
  
```sh  
make  all-recursive  
make[1]: Entering directory '/home/rketka/src/bagel-1.2.2/obj'  
Making all in src  
make[2]: Entering directory '/home/rketka/src/bagel-1.2.2/obj/src'  
Making all in util  
make[3]: Entering directory '/home/rketka/src/bagel-1.2.2/obj/src/util'  
Making all in parallel  
make[4]: Entering directory '/home/rketka/src/bagel-1.2.2/obj/src/util/parallel'  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT process.lo -MD -MP -MF .deps/process.Tpo -c -o process.lo ../../../../src/util/parallel/process.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT mpi_interface.lo -MD -MP -MF .deps/mpi_interface.Tpo -c -o mpi_interface.lo ../../../../src/util/parallel/mpi_interface.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT rmawindow.lo -MD -MP -MF .deps/rmawindow.Tpo -c -o rmawindow.lo ../../../../src/util/parallel/rmawindow.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT resources.lo -MD -MP -MF .deps/resources.Tpo -c -o resources.lo ../../../../src/util/parallel/resources.cc  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT mpi_interface.lo -MD -MP -MF .deps/mpi_interface.Tpo -c ../../../../src/util/parallel/mpi_interface.cc  -fPIC -DPIC -o .libs/mpi_interface.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT rmawindow.lo -MD -MP -MF .deps/rmawindow.Tpo -c ../../../../src/util/parallel/rmawindow.cc  -fPIC -DPIC -o .libs/rmawindow.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT process.lo -MD -MP -MF .deps/process.Tpo -c ../../../../src/util/parallel/process.cc  -fPIC -DPIC -o .libs/process.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/parallel -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT resources.lo -MD -MP -MF .deps/resources.Tpo -c ../../../../src/util/parallel/resources.cc  -fPIC -DPIC -o .libs/resources.o  
mv -f .deps/resources.Tpo .deps/resources.Plo  
mv -f .deps/process.Tpo .deps/process.Plo  
mv -f .deps/rmawindow.Tpo .deps/rmawindow.Plo  
mv -f .deps/mpi_interface.Tpo .deps/mpi_interface.Plo  
/bin/bash ../../../libtool  --tag=CXX   --mode=link mpicxx -I../../../.. -DNDEBUG -O3 -mavx  -L/home/rketka/src/boost/lib  -o libbagel_parallel.la  process.lo mpi_interface.lo rmawindow.lo resources.lo  -lmkl_scalapack_lp64 -lmkl_blacs_intelmpi_lp64 -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -liomp5 -lpthread -lm -lboost_regex -lboost_unit_test_framework -lboost_serialization    
libtool: link: ar cru .libs/libbagel_parallel.a .libs/process.o .libs/mpi_interface.o .libs/rmawindow.o .libs/resources.o   
ar: `u' modifier ignored since `D' is the default (see `U')  
libtool: link: ranlib .libs/libbagel_parallel.a  
libtool: link: ( cd ".libs" && rm -f "libbagel_parallel.la" && ln -s "../libbagel_parallel.la" "libbagel_parallel.la" )  
make[4]: Leaving directory '/home/rketka/src/bagel-1.2.2/obj/src/util/parallel'  
Making all in io  
make[4]: Entering directory '/home/rketka/src/bagel-1.2.2/obj/src/util/io'  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT moldenin.lo -MD -MP -MF .deps/moldenin.Tpo -c -o moldenin.lo ../../../../src/util/io/moldenin.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT moldenout.lo -MD -MP -MF .deps/moldenout.Tpo -c -o moldenout.lo ../../../../src/util/io/moldenout.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT moldenio.lo -MD -MP -MF .deps/moldenio.Tpo -c -o moldenio.lo ../../../../src/util/io/moldenio.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT molden_transforms.lo -MD -MP -MF .deps/molden_transforms.Tpo -c -o molden_transforms.lo ../../../../src/util/io/molden_transforms.cc  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT dfpcmo.lo -MD -MP -MF .deps/dfpcmo.Tpo -c -o dfpcmo.lo ../../../../src/util/io/dfpcmo.cc  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT molden_transforms.lo -MD -MP -MF .deps/molden_transforms.Tpo -c ../../../../src/util/io/molden_transforms.cc  -fPIC -DPIC -o .libs/molden_transforms.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT moldenin.lo -MD -MP -MF .deps/moldenin.Tpo -c ../../../../src/util/io/moldenin.cc  -fPIC -DPIC -o .libs/moldenin.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT dfpcmo.lo -MD -MP -MF .deps/dfpcmo.Tpo -c ../../../../src/util/io/dfpcmo.cc  -fPIC -DPIC -o .libs/dfpcmo.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT moldenio.lo -MD -MP -MF .deps/moldenio.Tpo -c ../../../../src/util/io/moldenio.cc  -fPIC -DPIC -o .libs/moldenio.o  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/io -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DNDEBUG -O3 -mavx -MT moldenout.lo -MD -MP -MF .deps/moldenout.Tpo -c ../../../../src/util/io/moldenout.cc  -fPIC -DPIC -o .libs/moldenout.o  
mv -f .deps/dfpcmo.Tpo .deps/dfpcmo.Plo  
mv -f .deps/moldenio.Tpo .deps/moldenio.Plo  
mv -f .deps/moldenout.Tpo .deps/moldenout.Plo  
mv -f .deps/molden_transforms.Tpo .deps/molden_transforms.Plo  
mv -f .deps/moldenin.Tpo .deps/moldenin.Plo  
/bin/bash ../../../libtool  --tag=CXX   --mode=link mpicxx -I../../../.. -DNDEBUG -O3 -mavx  -L/home/rketka/src/boost/lib  -o libbagel_io.la  moldenin.lo moldenout.lo moldenio.lo molden_transforms.lo dfpcmo.lo  -lmkl_scalapack_lp64 -lmkl_blacs_intelmpi_lp64 -lmkl_intel_lp64 -lmkl_intel_thread -lmkl_core -liomp5 -lpthread -lm -lboost_regex -lboost_unit_test_framework -lboost_serialization    
libtool: link: ar cru .libs/libbagel_io.a .libs/moldenin.o .libs/moldenout.o .libs/moldenio.o .libs/molden_transforms.o .libs/dfpcmo.o   
ar: `u' modifier ignored since `D' is the default (see `U')  
libtool: link: ranlib .libs/libbagel_io.a  
libtool: link: ( cd ".libs" && rm -f "libbagel_io.la" && ln -s "../libbagel_io.la" "libbagel_io.la" )  
make[4]: Leaving directory '/home/rketka/src/bagel-1.2.2/obj/src/util/io'  
Making all in input  
make[4]: Entering directory '/home/rketka/src/bagel-1.2.2/obj/src/util/input'  
/bin/bash ../../../libtool  --tag=CXX   --mode=compile mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/input -I../../..   -I/home/rketka/src/boost/include -I../../../.. -DBASIS_DIR=\"/usr/local/bagel/share\" -DNDEBUG -O3 -mavx -MT libbagel_input_la-input.lo -MD -MP -MF .deps/libbagel_input_la-input.Tpo -c -o libbagel_input_la-input.lo `test -f 'input.cc' || echo '../../../../src/util/input/'`input.cc  
libtool: compile:  mpicxx -DHAVE_CONFIG_H -I. -I../../../../src/util/input -I../../.. -I/home/rketka/src/boost/include -I../../../.. -DBASIS_DIR=\"/usr/local/bagel/share\" -DNDEBUG -O3 -mavx -MT libbagel_input_la-input.lo -MD -MP -MF .deps/libbagel_input_la-input.Tpo -c ../../../../src/util/input/input.cc  -fPIC -DPIC -o .libs/libbagel_input_la-input.o  
In file included from ../../../../src/util/serialization.h:46,  
                 from ../../../../src/util/input/input.h:31,  
                 from ../../../../src/util/input/input.cc:27:  
/home/rketka/src/boost/include/boost/property_tree/ptree_serialization.hpp: In function ‘void boost::property_tree::detail::load_children(Archive&, boost::property_tree::basic_ptree<K, D, C>&)’:  
/home/rketka/src/boost/include/boost/property_tree/ptree_serialization.hpp:66:24: error: ‘library_version_type’ in namespace ‘bsa’ does not name a type  
   66 |             const bsa::library_version_type library_version(  
      |                        ^~~~~~~~~~~~~~~~~~~~  
/home/rketka/src/boost/include/boost/property_tree/ptree_serialization.hpp:69:21: error: ‘library_version_type’ is not a member of ‘bsa’; did you mean ‘boost::serialization::library_version_type’?  
   69 |             if(bsa::library_version_type(3) < library_version){  
      |                     ^~~~~~~~~~~~~~~~~~~~  
In file included from /home/rketka/src/boost/include/boost/serialization/vector.hpp:28,  
                 from ../../../../src/util/serialization.h:39,  
                 from ../../../../src/util/input/input.h:31,  
                 from ../../../../src/util/input/input.cc:27:  
/home/rketka/src/boost/include/boost/serialization/library_version_type.hpp:36:7: note: ‘boost::serialization::library_version_type’ declared here  
   36 | class library_version_type {  
      |       ^~~~~~~~~~~~~~~~~~~~  
In file included from ../../../../src/util/serialization.h:46,  
                 from ../../../../src/util/input/input.h:31,  
                 from ../../../../src/util/input/input.cc:27:  
/home/rketka/src/boost/include/boost/property_tree/ptree_serialization.hpp:69:47: error: ‘library_version’ was not declared in this scope  
   69 |             if(bsa::library_version_type(3) < library_version){  
      |                                               ^~~~~~~~~~~~~~~  
make[4]: *** [Makefile:409: libbagel_input_la-input.lo] Error 1  
make[4]: Leaving directory '/home/rketka/src/bagel-1.2.2/obj/src/util/input'  
make[3]: *** [Makefile:462: all-recursive] Error 1  
make[3]: Leaving directory '/home/rketka/src/bagel-1.2.2/obj/src/util'  
make[2]: *** [Makefile:625: all-recursive] Error 1  
make[2]: Leaving directory '/home/rketka/src/bagel-1.2.2/obj/src'  
make[1]: *** [Makefile:424: all-recursive] Error 1  
make[1]: Leaving directory '/home/rketka/src/bagel-1.2.2/obj'  
make: *** [Makefile:356: all] Error 2  
```  
  
I am using boost v.1.78.0 (latest stable release) which was compiled via bootstrap + b2 (g++ 10.3 default compiler) on Ubuntu 20.04. Not sure if this is a bug in boost or version incompatibility between bagel and boost. Any ideas to solve this issue?

---

## Comment 1

> Username: jngrad  
> Created at: 2022-02-22 09:31:42 UTC  
> Url: https://github.com/boostorg/property_tree/issues/96#issuecomment-1047596158  

Looking at the compiler warnings, it seems Bagel is missing an `#include <boost/archive/basic_archive.hpp>` before the `#include <boost/property_tree/ptree_serialization.hpp>`. More details in qsimulate-open/bagel#229. I haven't tried it, but you should be able to patch Bagel 1.2.2 by applying the change in qsimulate-open/bagel/commit@4c0ab211.

---

## Comment 2

> Username: robertramey  
> Created at: 2022-02-22 23:49:54 UTC  
> Url: https://github.com/boostorg/property_tree/issues/96#issuecomment-1048319790  

I'm pretty sure this is due to a breaking change f72b9fc8d953a5dd39615535b5c6bab5b8be42fe put into the develop branch on 26 Sep, 2020.  cheng the file ptree_serialization to:  
[ptree_serialization.hpp.zip](https://github.com/boostorg/serialization/files/8120932/ptree_serialization.hpp.zip)

---

## Comment 3

> Username: ashtum  
> Created at: 2023-11-15 10:05:26 UTC  
> Url: https://github.com/boostorg/property_tree/issues/96#issuecomment-1812149001  

Fixed in https://github.com/boostorg/property_tree/commit/532853791b650c0fe2c301e1f72e4b064e1506cf.
