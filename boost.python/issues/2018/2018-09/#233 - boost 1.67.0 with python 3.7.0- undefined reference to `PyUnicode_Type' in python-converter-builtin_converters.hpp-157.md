# #233 - boost 1.67.0 with python 3.7.0: undefined reference to `PyUnicode_Type' in python/converter/builtin_converters.hpp:157 [Closed]

> Username: ToonWeyens  
> Created at: 2018-09-24 14:21:36 UTC  
> Updated at: 2018-09-24 15:48:22 UTC  
> Closed at: 2018-09-24 15:48:22 UTC  
> Url: https://github.com/boostorg/python/issues/233  

I built boost 1.67.0 using the commands  
  
    ./bootstrap.sh --exec-prefix=install/ --with-libraries=graph,python,system,filesystem --libdir=install/lib/ --includedir=install/include  
  
using, furthermore, python 3.7 (which I installed through pyenv using `CONFIGURE_OPTS=--enable-shared pyenv install 3.7.0`) through commands  
  
    import python ;  
    if ! [ python.configured ]  
    {  
        using python : 3.7 : /home/toon/.pyenv/versions/3.7.0 : /home/toon/.pyenv/versions/3.7.0/include/python3.7m ;  
    }  
  
inside the `project-config.jam` file.  
  
Later, when I try to compile and llink my own C++ project in `py_interface.cpp`, I get the following error:  
    g++ -c -Wall -std=c++11 -g -rdynamic -fpic -c py_interface.cpp  
    g++ ESPPRC.o input.o py_interface.o -std=c++11 -g -rdynamic -L/home/toon/.pyenv/versions/3.7.0/lib -L/opt/boost_1_67_0/install/lib -lpython3 -lboost_graph -lboost_numpy37 -lboost_python37 -lboost_system -lboost_filesystem  -Wl,--no-undefined -shared -o libESPPRC.so  
    py_interface.o: In function `boost::python::to_python_value<char const* const&>::get_pytype() const':  
    /opt/boost_1_67_0/install/include/boost/python/converter/builtin_converters.hpp:158: undefined reference to `PyUnicode_Type'  
    py_interface.o: In function `boost::python::api::object::object()':  
    /opt/boost_1_67_0/install/include/boost/python/object_core.hpp:400: undefined reference to `_Py_NoneStruct'  
    collect2: error: ld returned 1 exit status  
      
The boost libraries are:  
  
    lib/libboost_filesystem.so.1.67.0  
    lib/libboost_graph.so.1.67.0  
    lib/libboost_numpy37.so.1.67.0  
    lib/libboost_python37.so.1.67.0  
    lib/libboost_regex.so.1.67.0  
    lib/libboost_system.so.1.67.0  
  
I read somewhere that the problem might be cause by different versions of python, but to the best of my ability, I checked this and I don't think so.  
  
I used the following export in my makefile, by the way:  
  
    export CPLUS_INCLUDE_PATH=[HOME]/.pyenv/versions/3.7.0/include/python3.7m/:/[BOOST DIR]/install/include  
  
Also, I looked for `PyUnicode_Type`, and found it defined in `unicodeobject.h` in the python 3.7.0 include directory. Even when I manually include this file in `builtin_converters.hpp`, right before where the error happens, it still doesn't work...  
  
Could anyone point me in the right direction? I appreciate all help!

---

## Comment 1

> Username: ToonWeyens  
> Created at: 2018-09-24 15:48:15 UTC  
> Url: https://github.com/boostorg/python/issues/233#issuecomment-424023900  

Okay it looks like you have to import the `--with-pymalloc` version:  
  
    -lpython3.7m -lboost_graph -lboost_numpy37 -lboost_python37 -lboost_system -lboost_filesystem   
  
instead of  
  
    -lpython3 -lboost_graph -lboost_numpy37 -lboost_python37 -lboost_system -lboost_filesystem   
  
It looks like things are working better now but I'll need some more to test.
