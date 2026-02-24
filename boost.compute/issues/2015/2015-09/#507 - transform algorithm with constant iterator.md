# #507 - transform algorithm with constant iterator [Open]

> Username: jpola  
> Created at: 2015-09-10 16:51:02 UTC  
> Updated at: 2017-03-22 17:40:55 UTC  
> Url: https://github.com/boostorg/compute/issues/507  

My tests revealed that when using constant iterator tupled and zipped gives segfault.  
Here is the code which shows that:  
  
``` c++  
#include <boost/compute/functional.hpp>  
#include <boost/compute/buffer.hpp>  
#include <boost/compute/container/vector.hpp>  
#include <boost/compute/iterator/buffer_iterator.hpp>  
#include <boost/compute/iterator/constant_iterator.hpp>  
#include <boost/compute/iterator/zip_iterator.hpp>  
#include <boost/compute/algorithm/transform.hpp>  
#include <boost/tuple/tuple.hpp>  
  
  
  
int main (int argc, const char* argv[])  
{   
    namespace compute = boost::compute;  
  
try {  
    int size = 128;  
    compute::vector<float> xVector (size, 1.f);  
    compute::vector<float> yVector (size, 2.f);  
    compute::vector<float> zVector (size, 3.f);  
  
    compute::vector<float> rVector (size, 0.f);  
  
        BOOST_COMPUTE_FUNCTION(float, function_tuple_3, (boost::tuple<float, float, float> t),  
           {  
                return boost_tuple_get(t, 0);  
           });  
        BOOST_COMPUTE_FUNCTION(float, function_tuple_2, (boost::tuple<float, float> t),  
           {  
                return boost_tuple_get(t, 0);  
           });  
  
  
    /*This was my original code where I found the bug*/  
    /*   
        compute::transform(  
                    //first  
                    compute::make_zip_iterator(  
                        boost::make_tuple(  
                            xVector.begin(),  
                yVector.begin(),  
                //zVector.begin()  //when using zVector instead of constant_iterator it is working correctly.  
                            compute::make_constant_iterator<float>(4.f)  
                            )  
                        ),  
                    //last  
                    compute::make_zip_iterator(  
                        boost::make_tuple(  
                xVector.end(),  
                yVector.end(),  
                //zVector.end()  // change it here accordingly  
                            compute::make_constant_iterator<float>(4.f, size)  
                            )  
                        ),  
  
                    rVector.begin(),  
                    //function  
                    function_tuple_3  
                    );  
    */  
  
    /* when not zipping constant iterator it seems fine */  
    /*    
    compute::transform(compute::make_constant_iterator<float>(4.f),  
            compute::make_constant_iterator<float>(4.f, size),  
            rVector.begin(),  
            compute::identity<float>()  
    );  
    */  
  
    /* when start to zip it, program crashes */  
    compute::transform(  
            compute::make_zip_iterator(  
                boost::make_tuple(  
                    compute::make_constant_iterator<float>(4.f),  
                    compute::make_constant_iterator<float>(4.f)  
                )  
            ),  
            compute::make_zip_iterator(  
                boost::make_tuple(  
                    compute::make_constant_iterator<float>(4.f, size),  
                    compute::make_constant_iterator<float>(4.f, size)  
                )  
            ),  
            rVector.begin(),  
            function_tuple_2  
    );  
    }  
    catch (compute::opencl_error &err)  
    {  
        std::cerr << err.what() << std::endl;  
    return -1;  
    }  
    catch (...)  
    {  
        std::cerr << "Boost compute failed with transforn operation" << std::endl;  
        return -2;  
    }  
return 0;  
}  
  
```  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-09-15 17:51:56 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-140480783  

Hmm, I ran your code on my laptop (Macbook Pro with Geforce GT 750M) and didn't get a crash. What platform/GPU are you using? Does it throw an exception or give a segfault? If a segfault, could you run it through gdb and post the backtrace? Thanks!

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-09-15 17:57:40 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-140482443  

I've made a very simple test case in PR #512, can you try that out and let me know if it crashes on your machine?

---

## Comment 3

> Username: jpola  
> Created at: 2015-09-15 18:16:19 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-140488286  

I've tested PR #512 it is working. The issue seems to be related to BOOST_COMPUTE_FUNCTION  i defined in this example not to the constant iterator itself.

---

## Comment 4

> Username: jpola  
> Created at: 2015-09-15 18:45:01 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-140495446  

I modified the test you have prepared:  
  
``` c++  
BOOST_AUTO_TEST_CASE(zip_constant_iterator)  
{  
    compute::vector<int> result(4, context);  
    compute::vector<int> x(4, context);  
  
 BOOST_COMPUTE_FUNCTION(int, fun, (boost::tuple<int, int> x),  
           {  
                                   return 10;  
           });  
  
    compute::transform(  
        compute::make_zip_iterator(  
            boost::make_tuple(  
        x.begin(),  
        compute::make_constant_iterator(7)  
            )  
        ),  
        compute::make_zip_iterator(  
            boost::make_tuple(  
        x.end(),  
        compute::make_constant_iterator(7, result.size())  
            )  
        ),  
        result.begin(),  
    fun,  
        queue  
    );  
  
    CHECK_RANGE_EQUAL(int, 4, result, (7, 7, 7, 7));  
}  
```  
  
this is the backtrace returned by gdb:  
  
```  
Starting program: /home/jpola/Projects/boost_compute/bin/test/test_zip_iterator   
Traceback (most recent call last):  
  File "/usr/share/gdb/auto-load/usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.19-gdb.py", line 63, in <module>  
    from libstdcxx.v6.printers import register_libstdcxx_printers  
ImportError: No module named 'libstdcxx'  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
[New Thread 0x7ffff1b11700 (LWP 9683)]  
  
Program received signal SIGSEGV, Segmentation fault.  
0x00007ffff50a83e0 in ?? () from /usr/lib/libamdocl64.so  
#0  0x00007ffff50a83e0 in ?? () from /usr/lib/libamdocl64.so  
#1  0x00007ffff5079a0d in ?? () from /usr/lib/libamdocl64.so  
#2  0x00007ffff4d0afcf in ?? () from /usr/lib/libamdocl64.so  
#3  0x00007ffff4d0e85a in ?? () from /usr/lib/libamdocl64.so  
#4  0x00007ffff5099fbf in ?? () from /usr/lib/libamdocl64.so  
#5  0x00007ffff509a0ae in ?? () from /usr/lib/libamdocl64.so  
#6  0x00007ffff509a1e8 in ?? () from /usr/lib/libamdocl64.so  
#7  0x00007ffff3a0ee3c in ?? () from /usr/lib/libamdocl64.so  
#8  0x00007ffff3a0f06d in ?? () from /usr/lib/libamdocl64.so  
#9  0x00007ffff3a0e553 in ?? () from /usr/lib/libamdocl64.so  
#10 0x00007ffff3a1163f in ?? () from /usr/lib/libamdocl64.so  
#11 0x00007ffff3a14c77 in ?? () from /usr/lib/libamdocl64.so  
#12 0x00007ffff39cde49 in aclCompile () from /usr/lib/libamdocl64.so  
#13 0x00007ffff316dd65 in ?? () from /usr/lib/libamdocl64.so  
#14 0x00007ffff319100c in ?? () from /usr/lib/libamdocl64.so  
#15 0x00007ffff313dfbf in ?? () from /usr/lib/libamdocl64.so  
#16 0x00007ffff314dac0 in ?? () from /usr/lib/libamdocl64.so  
#17 0x00007ffff312f239 in clBuildProgram () from /usr/lib/libamdocl64.so  
#18 0x000000000047533e in boost::compute::program::build (this=0x7fffffffa2a0, options=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/program.hpp:255  
#19 0x00000000004755bc in boost::compute::program::build_with_source (source=..., context=..., options=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/program.hpp:546  
#20 0x000000000047847c in boost::compute::program_cache::get_or_build (this=0x8c7290, key=..., options=..., source=..., context=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/utility/program_cache.hpp:136  
#21 0x0000000000479070 in boost::compute::detail::meta_kernel::compile (this=0x7fffffffa700, context=..., options=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/detail/meta_kernel.hpp:347  
#22 0x0000000000479455 in boost::compute::detail::meta_kernel::exec_1d (this=0x7fffffffa700, queue=..., global_work_offset=0, global_work_size=128, local_work_size=128)  
    at /home/jpola/Projects/boost_compute/compute/include/boost/compute/detail/meta_kernel.hpp:655  
#23 0x0000000000497fc0 in boost::compute::detail::copy_kernel<boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int> >::exec(boost::compute::command_queue&) (this=0x7fffffffa700, queue=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/algorithm/detail/copy_on_device.hpp:94  
#24 0x0000000000492deb in boost::compute::detail::copy_on_device<boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int> >(boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int>, boost::compute::command_queue&) (first=..., last=..., result=..., queue=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/algorithm/detail/copy_on_device.hpp:115  
#25 0x000000000048e07c in boost::compute::detail::dispatch_copy<boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int> >(boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int>, boost::compute::command_queue&, boost::enable_if<boost::mpl::and_<boost::compute::is_device_iterator<boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> > >, boost::compute::is_device_iterator<boost::compute::buffer_iterator<int> >, boost::mpl::not_<boost::compute::detail::can_copy_with_copy_buffer<boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int> > >, mpl_::bool_<true>, mpl_::bool_<true> >, void>::type*) (first=..., last=..., result=...,   
    queue=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/algorithm/copy.hpp:167  
#26 0x0000000000487289 in boost::compute::copy<boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int> >(boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::transform_iterator<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >, boost::compute::buffer_iterator<int>, boost::compute::command_queue&) (first=...,   
    last=..., result=..., queue=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/algorithm/copy.hpp:342  
#27 0x0000000000480678 in boost::compute::transform<boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::buffer_iterator<int>, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)> >(boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::zip_iterator<boost::tuples::tuple<boost::compute::buffer_iterator<int>, boost::compute::constant_iterator<int>, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type> >, boost::compute::buffer_iterator<int>, boost::compute::function<int (boost::tuples::tuple<int, int, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type, boost::tuples::null_type>)>, boost::compute::command_queue&) (first=..., last=..., result=..., op=..., queue=...) at /home/jpola/Projects/boost_compute/compute/include/boost/compute/algorithm/transform.hpp:45  
#28 0x0000000000472190 in compute_test::zip_constant_iterator::test_method (this=0x7fffffffb2c0) at /home/jpola/Projects/boost_compute/compute/test/test_zip_iterator.cpp:232  
#29 0x0000000000471f88 in compute_test::zip_constant_iterator_invoker () at /home/jpola/Projects/boost_compute/compute/test/test_zip_iterator.cpp:206  
#30 0x00000000004a0e74 in boost::unit_test::ut_detail::invoker<boost::unit_test::ut_detail::unused>::invoke<void (*)()> (this=0x7fffffffb34f, f=@0x6c59c8: 0x471f67 <compute_test::zip_constant_iterator_invoker()>)  
    at /home/jpola/Projects/ClMath_ClSparse/jpola/build/Externals/Boost/package/include/boost/test/utils/callback.hpp:56  
#31 0x00000000004a0a96 in boost::unit_test::ut_detail::callback0_impl_t<boost::unit_test::ut_detail::unused, void (*)()>::invoke (this=0x6c59c0)  
    at /home/jpola/Projects/ClMath_ClSparse/jpola/build/Externals/Boost/package/include/boost/test/utils/callback.hpp:89  
#32 0x00007ffff79981f1 in ?? () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#33 0x00007ffff7973546 in boost::execution_monitor::catch_signals(boost::unit_test::callback0<int> const&) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#34 0x00007ffff7973d83 in boost::execution_monitor::execute(boost::unit_test::callback0<int> const&) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#35 0x00007ffff79982f2 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::unit_test::test_case const&) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#36 0x00007ffff7981f94 in boost::unit_test::framework_impl::visit(boost::unit_test::test_case const&) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#37 0x00007ffff79b0d23 in boost::unit_test::traverse_test_tree(boost::unit_test::test_suite const&, boost::unit_test::test_tree_visitor&) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#38 0x00007ffff79b0d23 in boost::unit_test::traverse_test_tree(boost::unit_test::test_suite const&, boost::unit_test::test_tree_visitor&) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#39 0x00007ffff797d4ba in boost::unit_test::framework::run(unsigned long, bool) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#40 0x00007ffff7995ed4 in boost::unit_test::unit_test_main(bool (*)(), int, char**) () from /usr/lib/x86_64-linux-gnu/libboost_unit_test_framework.so.1.54.0  
#41 0x000000000046f926 in main (argc=1, argv=0x7fffffffc198) at /home/jpola/Projects/ClMath_ClSparse/jpola/build/Externals/Boost/package/include/boost/test/unit_test.hpp:59  
```  
  
I'm using AMD Device:  
  
```  
 Platform Name:              AMD Accelerated Parallel Processing  
Number of devices:               2  
  Device Type:                   CL_DEVICE_TYPE_GPU  
  Vendor ID:                     1002h  
  Board name:                    AMD Radeon R9 200 Series  
  Device Topology:               PCI[ B#1, D#0, F#0 ]  
  Max compute units:                 20  
  Max work items dimensions:             3  
    Max work items[0]:               256  
    Max work items[1]:               256  
    Max work items[2]:               256  
  Max work group size:               256  
  Preferred vector width char:           4  
  Preferred vector width short:          2  
  Preferred vector width int:            1  
  Preferred vector width long:           1  
  Preferred vector width float:          1  
  Preferred vector width double:         1  
  Native vector width char:          4  
  Native vector width short:             2  
  Native vector width int:           1  
  Native vector width long:          1  
  Native vector width float:             1  
  Native vector width double:            1  
  Max clock frequency:               1070Mhz  
  Address bits:                  64  
  Max memory allocation:             2765094912  
  Image support:                 Yes  
  Max number of images read arguments:       128  
  Max number of images write arguments:      8  
  Max image 2D width:                16384  
  Max image 2D height:               16384  
  Max image 3D width:                2048  
  Max image 3D height:               2048  
  Max image 3D depth:                2048  
  Max samplers within kernel:            16  
  Max size of kernel argument:           1024  
  Alignment (bits) of base address:      2048  
  Minimum alignment (bytes) for any datatype:    128  
  Single precision floating point capability  
    Denorms:                     No  
    Quiet NaNs:                  Yes  
    Round to nearest even:           Yes  
    Round to zero:               Yes  
    Round to +ve and infinity:           Yes  
    IEEE754-2008 fused multiply-add:         Yes  
  Cache type:                    Read/Write  
  Cache line size:               64  
  Cache size:                    16384  
  Global memory size:                3832545280  
  Constant buffer size:              65536  
  Max number of constant args:           8  
  Local memory type:                 Scratchpad  
  Local memory size:                 32768  
  Max pipe arguments:                0  
  Max pipe active reservations:          0  
  Max pipe packet size:              0  
  Max global variable size:          0  
  Max global variable preferred total size:  0  
  Max read/write image args:             0  
  Max on device events:              0  
  Queue on device max size:          0  
  Max on device queues:              0  
  Queue on device preferred size:        0  
  SVM capabilities:                
    Coarse grain buffer:             No  
    Fine grain buffer:               No  
    Fine grain system:               No  
    Atomics:                     No  
  Preferred platform atomic alignment:       0  
  Preferred global atomic alignment:         0  
  Preferred local atomic alignment:      0  
  Kernel Preferred work group size multiple:     64  
  Error correction support:          0  
  Unified memory for Host and Device:        0  
  Profiling timer resolution:            1  
  Device endianess:              Little  
  Available:                     Yes  
  Compiler available:                Yes  
  Execution capabilities:                  
    Execute OpenCL kernels:          Yes  
    Execute native function:             No  
  Queue on Host properties:                
    Out-of-Order:                No  
    Profiling :                  Yes  
  Queue on Device properties:                  
    Out-of-Order:                No  
    Profiling :                  No  
  Platform ID:                   0x7effce10c630  
  Name:                      Pitcairn  
  Vendor:                    Advanced Micro Devices, Inc.  
  Device OpenCL C version:           OpenCL C 1.2   
  Driver version:                1702.3 (VM)  
  Profile:                   FULL_PROFILE  
  Version:                   OpenCL 1.2 AMD-APP (1702.3)  
  Extensions:                    cl_khr_fp64 cl_amd_fp64 cl_khr_global_int32_base_atomics cl_khr_global_int32_extended_atomics cl_khr_local_int32_base_atomics cl_khr_local_int32_extended_atomics cl_khr_int64_base_atomics cl_khr_int64_extended_atomics cl_khr_3d_image_writes cl_khr_byte_addressable_store cl_khr_gl_sharing cl_ext_atomic_counters_32 cl_amd_device_attribute_query cl_amd_vec3 cl_amd_printf cl_amd_media_ops cl_amd_media_ops2 cl_amd_popcnt cl_khr_image2d_from_buffer cl_khr_spir cl_khr_gl_event   
  
```

---

## Comment 5

> Username: jszuppe  
> Created at: 2015-09-16 10:46:44 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-140703179  

This is the kernel generated from @jpola modification of @kylelutz test:  
  
``` cpp  
#define boost_pair_type(t1, t2) _pair_ ## t1 ## _ ## t2 ## _t  
#define boost_pair_get(x, n) (n == 0 ? x.first ## x.second)  
#define boost_make_pair(t1, x, t2, y) (boost_pair_type(t1, t2)) { x, y }  
#define boost_tuple_get(x, n) (x.v ## n)  
typedef struct {  
    int v0;  
    int v1;  
} boost_tuple_int_int_t;  
  
inline int fun(boost_tuple_int_int_t x){ return 10; }  
  
  
__kernel void copy(uint count, __global int* _buf0, __global int* _buf1)  
{  
uint index = get_local_id(0) + (512 * get_group_id(0));  
for(uint i = 0; i < 4; i++){  
    if(index < count){  
_buf0[index]=fun((boost_tuple_int_int_t){ _buf1[index], 7});  
        index += 128;  
    }  
}  
  
}  
```  
  
It crashes. It causes even CodeXL Kernel Analyzer  kernel to segfault. If I remove `boost_tuple_int_int_t` struct definition and replace it with `int2` type kernel works in CodeXL Analyzer.  
  
``` cpp  
typedef struct {  
    int v0;  
    int v1;  
} boost_tuple_int_int_t;  
```

---

## Comment 6

> Username: jszuppe  
> Created at: 2015-09-16 11:30:43 UTC  
> Updated at: 2015-09-16 11:31:04 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-140713228  

Replacing  
  
``` cpp  
_buf0[index]=fun((boost_tuple_int_int_t){ _buf1[index], 7});  
```  
  
with  
  
``` cpp  
boost_tuple_int_int_t value = {_buf1[index], 7};  
_buf0[index]=fun(value);  
```  
  
makes it work for me on CodeXL Analyze Mode. I think `(boost_tuple_int_int_t){ _buf1[index], 7}` should work. It would be great to test it with NVIDIA or Intel OpenCL compiler.

---

## Comment 7

> Username: kylelutz  
> Created at: 2015-09-17 15:07:06 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-141116152  

Oh yeah, we've hit this issue before. The AMD OpenCL compiler seems to have problems when assigning aggregate types (structs) from temporary values (which should be perfectly valid and is supported by Intel/NVIDIA).  
  
A long term fix may be to generate smarter code which does per-member assignment or something like `memcpy()` the raw struct data though a `void *`. We'd have to see if any of these have performance impacts and maybe only generate this code when necessary for AMD devices.

---

## Comment 8

> Username: jszuppe  
> Created at: 2015-09-17 15:34:38 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-141124214  

Has anyone posted this bug on AMD Developer Community?

---

## Comment 9

> Username: kylelutz  
> Created at: 2015-09-19 16:46:06 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-141687070  

Yeah, there was a post made by @ddemidov  on the AMD OpenCL forums a while back (https://community.amd.com/thread/166622). I don't think the issue has been resolved.

---

## Comment 10

> Username: jszuppe  
> Created at: 2015-09-19 17:03:02 UTC  
> Updated at: 2015-09-22 10:58:37 UTC  
> Url: https://github.com/boostorg/compute/issues/507#issuecomment-141688004  

Well, they marked it resolved. I'll make new post for this bug.  
  
Edit#1: https://community.amd.com/message/2673857
