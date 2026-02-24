# #342 - attribute appertains to a friend declaration that is not a definition [Open]

> Username: BenniProbst  
> Created at: 2021-05-31 18:05:51 UTC  
> Updated at: 2021-05-31 18:05:51 UTC  
> Url: https://github.com/boostorg/thread/issues/342  

====================[ Build | Kodierungstheorie | Debug ]=======================  
/usr/local/bin/cmake --build /home/benjamin-elias/CLionProjects/Kodierungstheorie/cmake-build-debug --target Kodierungstheorie -- -j 6  
Scanning dependencies of target Kodierungstheorie  
[ 16%] Building CXX object CMakeFiles/Kodierungstheorie.dir/main.cpp.o  
In Datei, eingebunden von /home/benjamin-elias/Downloads/boost_1_76_0/boost/thread/thread_only.hpp:22,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/thread/thread.hpp:12,  
                 von /home/benjamin-elias/Downloads/boost_1_76_0/boost/thread.hpp:13,  
                 von /home/benjamin-elias/CLionProjects/Kodierungstheorie/main.cpp:2:  
/home/benjamin-elias/Downloads/boost_1_76_0/boost/thread/detail/thread.hpp:641:37: Fehler: attribute appertains to a friend declaration that is not a definition  
  641 |         friend id BOOST_THREAD_DECL this_thread::get_id() BOOST_NOEXCEPT;  
      |                                     ^~~~~~~~~~~  
make[3]: *** [CMakeFiles/Kodierungstheorie.dir/build.make:72: CMakeFiles/Kodierungstheorie.dir/main.cpp.o] Fehler 1  
make[2]: *** [CMakeFiles/Makefile2:85: CMakeFiles/Kodierungstheorie.dir/all] Fehler 2  
make[1]: *** [CMakeFiles/Makefile2:92: CMakeFiles/Kodierungstheorie.dir/rule] Fehler 2  
make: *** [Makefile:124: Kodierungstheorie] Fehler 2
