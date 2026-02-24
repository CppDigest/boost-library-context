# #182 - pmr cannot handle enum typedefs [Closed]

> Username: BenniProbst  
> Created at: 2021-03-21 18:40:53 UTC  
> Updated at: 2021-04-22 14:12:44 UTC  
> Closed at: 2021-04-22 14:12:44 UTC  
> Url: https://github.com/boostorg/container/issues/182  

/usr/bin/ld: CMakeFiles/Kodierungstheorie.dir/main.cpp.o: in function `boost::container::pmr::polymorphic_allocator<boost::container::dtl::pair<intern_enum, int> >::polymorphic_allocator()':  
/home/benjamin-elias/Downloads/boost_1_75_0/boost/container/pmr/polymorphic_allocator.hpp:46: undefined reference to `boost::container::pmr::get_default_resource()'  
/usr/bin/ld: CMakeFiles/Kodierungstheorie.dir/main.cpp.o: in function `boost::container::pmr::polymorphic_allocator<int>::polymorphic_allocator()':  
/home/benjamin-elias/Downloads/boost_1_75_0/boost/container/pmr/polymorphic_allocator.hpp:46: undefined reference to `boost::container::pmr::get_default_resource()'  
/usr/bin/ld: CMakeFiles/Kodierungstheorie.dir/main.cpp.o: in function `boost::container::pmr::polymorphic_allocator<boost::container::stable_vector_detail::node_base<void*>*>::polymorphic_allocator()':  
/home/benjamin-elias/Downloads/boost_1_75_0/boost/container/pmr/polymorphic_allocator.hpp:46: undefined reference to `boost::container::pmr::get_default_resource()'  
/usr/bin/ld: CMakeFiles/Kodierungstheorie.dir/main.cpp.o: in function `boost::container::pmr::polymorphic_allocator<boost::container::stable_vector_detail::node<int*> >::polymorphic_allocator()':  
/home/benjamin-elias/Downloads/boost_1_75_0/boost/container/pmr/polymorphic_allocator.hpp:46: undefined reference to `boost::container::pmr::get_default_resource()'  
/usr/bin/ld: CMakeFiles/Kodierungstheorie.dir/main.cpp.o: in function `boost::container::pmr::polymorphic_allocator<int*>::polymorphic_allocator()':  
/home/benjamin-elias/Downloads/boost_1_75_0/boost/container/pmr/polymorphic_allocator.hpp:46: undefined reference to `boost::container::pmr::get_default_resource()'  
/usr/bin/ld: CMakeFiles/Kodierungstheorie.dir/main.cpp.o:/home/benjamin-elias/Downloads/boost_1_75_0/boost/container/pmr/polymorphic_allocator.hpp:46: more undefined references to `boost::container::pmr::get_default_resource()' follow  
collect2: error: ld returned 1 exit status  
make[3]: *** [CMakeFiles/Kodierungstheorie.dir/build.make:103: Kodierungstheorie] Error 1  
make[2]: *** [CMakeFiles/Makefile2:95: CMakeFiles/Kodierungstheorie.dir/all] Error 2  
make[1]: *** [CMakeFiles/Makefile2:102: CMakeFiles/Kodierungstheorie.dir/rule] Error 2  
make: *** [Makefile:137: Kodierungstheorie] Error 2  
  
with enums  
  
typedef enum {  
	VECTOR, DEQUE, SLIST, LIST, AUTO  
} mode_enum;  
typedef enum {  
	VEC, DEQ, SL, L  
} intern_enum;  
  
the key of a pmr map should be converted to int to fit, if enum is inserted and type should be checked to be correct if retrieved

---

## Comment 1

> Username: BenniProbst  
> Created at: 2021-03-21 18:49:39 UTC  
> Url: https://github.com/boostorg/container/issues/182#issuecomment-803640397  

Main issue at  
typedef boost::container::pmr::flat_map<intern_enum, int> map_t;  
	map_t statistics{{static_cast<int>(VEC), 0},  
	                 {static_cast<int>(DEQ), 0},  
	                 {static_cast<int>(SL),  0},  
	                 {static_cast<int>(L),   0}};

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-04-21 08:13:01 UTC  
> Url: https://github.com/boostorg/container/issues/182#issuecomment-823871409  

I can't see any relationship between enums and linker errors. The linker complains because you need to link boost.container library (https://www.boost.org/doc/libs/1_76_0/doc/html/container.html#container.intro.introduction_building_container). Could you please provide a minimal test case and detail the environment (OS, compiler version, etc.)?

---

## Comment 3

> Username: BenniProbst  
> Created at: 2021-04-21 08:27:39 UTC  
> Url: https://github.com/boostorg/container/issues/182#issuecomment-823881193  

I use Linux mint 20.1 , GCC 10.2.2 . It was late, It's probably my fault. I could not call with an enum type out of list.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2021-04-22 14:12:44 UTC  
> Url: https://github.com/boostorg/container/issues/182#issuecomment-824878131  

Since I can't reproduce the bug I'm closing it, please reopen it if you manage to have a minimal test case showing the bug.
