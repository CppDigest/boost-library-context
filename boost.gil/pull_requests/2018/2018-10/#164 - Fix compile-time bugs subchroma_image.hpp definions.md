# #164 Fix compile-time bugs subchroma_image.hpp definions [Merged]

> Username: mloskot  
> Created at: 2018-10-25 05:32:26 UTC  
> Updated at: 2018-12-07 22:55:35 UTC  
> Merged at: 2018-12-07 22:55:14 UTC  
> Closed at: 2018-12-07 22:55:14 UTC  
> Url: https://github.com/boostorg/gil/pull/164  

Add missing typenamei in mpl::if_ condition result  
  
Restore BOOST_CXX14_CONSTEXPR in boost::algorithm::clamp function  
    - apparently, GCC 5.5.0 does not compile it with C++11 constexpr.  
  
Still not adding subchroma_image.cpp to toolbox test target input sources due to run-time failure.  
  
Depends on #176  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-10-28 13:51:24 UTC  
> Url: https://github.com/boostorg/gil/pull/164#issuecomment-433707037  

We're still waiting for boostorg/gil Travis but I think the build will be fine as it is for my fork https://travis-ci.org/mloskot/gil/builds/447353644. So please review @stefanseefeld & @chhenning

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-10-28 17:59:54 UTC  
> Updated_at: 2018-10-28 18:00:30 UTC  
> Url: https://github.com/boostorg/gil/pull/164#issuecomment-433727125  

UPDATE: In my fork, one Travis job `VARIANT=ubsan_undefined` is failing - https://travis-ci.org/mloskot/gil/jobs/447353654  
  
But, IMO, that is _not related to these changes_, or is it? The failure is possibly revealing a new bug:  
  
```  
====== BEGIN OUTPUT ======  
boost/gil/extension/toolbox/color_spaces/ycbcr.hpp:236:49: runtime error: -124.402 is outside the range of representable values of type 'unsigned char'  
    #0 0x454992 in void boost::gil::default_color_converter_impl<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t> >::operator()<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::range_c<int, 0, 3> > >, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > > >(boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::range_c<int, 0, 3> > > const&, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >&) const /home/travis/build/mloskot/boost-root/./boost/gil/extension/toolbox/color_spaces/ycbcr.hpp:236:49  
    #1 0x45205e in void boost::gil::default_color_converter::operator()<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::range_c<int, 0, 3> > >, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > > >(boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::range_c<int, 0, 3> > > const&, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >&) const /home/travis/build/mloskot/boost-root/./boost/gil/color_convert.hpp:287:9  
    #2 0x45205e in void boost::gil::color_convert<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::range_c<int, 0, 3> > >, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > > >(boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::ycbcr_709_color_space::y_t, boost::gil::ycbcr_709_color_space::cb_t, boost::gil::ycbcr_709_color_space::cr_t>, boost::mpl::range_c<int, 0, 3> > > const&, boost::gil::pixel<unsigned char, boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> > >&) /home/travis/build/mloskot/boost-root/./boost/gil/color_convert.hpp:297  
    #3 0x45205e in toolbox_tests::subchroma_image_test::test_method() /home/travis/build/mloskot/boost-root/libs/gil/toolbox/test/subchroma_image.cpp:41  
    #4 0x451b41 in toolbox_tests::subchroma_image_test_invoker() /home/travis/build/mloskot/boost-root/libs/gil/toolbox/test/subchroma_image.cpp:22:1  
    #5 0x7f0c9f76193a in boost::detail::forward::operator()() /home/travis/build/mloskot/boost-root/./boost/test/impl/execution_monitor.ipp:1312:32  
    #6 0x7f0c9f76193a in boost::detail::function::function_obj_invoker<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) /home/travis/build/mloskot/boost-root/./boost/function/function_template.hpp:172  
    #7 0x7f0c9f75bd84 in boost::execution_monitor::catch_signals(boost::function<int ()> const&) /home/travis/build/mloskot/boost-root/./boost/test/impl/execution_monitor.ipp:875:16  
    #8 0x7f0c9f75bfe8 in boost::execution_monitor::execute(boost::function<int ()> const&) /home/travis/build/mloskot/boost-root/./boost/test/impl/execution_monitor.ipp:1214:16  
    #9 0x7f0c9f75ddfe in boost::execution_monitor::vexecute(boost::function<void ()> const&) /home/travis/build/mloskot/boost-root/./boost/test/impl/execution_monitor.ipp:1321:5  
    #10 0x7f0c9f7a8b28 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) /home/travis/build/mloskot/boost-root/./boost/test/impl/unit_test_monitor.ipp:49:9  
    #11 0x7f0c9f76e215 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/travis/build/mloskot/boost-root/./boost/test/impl/framework.ipp:789:44  
    #12 0x7f0c9f76e3c5 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/travis/build/mloskot/boost-root/./boost/test/impl/framework.ipp:737:54  
    #13 0x7f0c9f76e3c5 in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) /home/travis/build/mloskot/boost-root/./boost/test/impl/framework.ipp:737:54  
    #14 0x7f0c9f767c6a in boost::unit_test::framework::run(unsigned long, bool) /home/travis/build/mloskot/boost-root/./boost/test/impl/framework.ipp:1631:29  
    #15 0x7f0c9f7a57bf in boost::unit_test::unit_test_main(bool (*)(), int, char**) /home/travis/build/mloskot/boost-root/./boost/test/impl/unit_test_main.ipp:247:9  
    #16 0x7f0c9e228f44 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21f44)  
    #17 0x40bfa6 in _start (/home/travis/build/mloskot/boost-root/bin.v2/libs/gil/toolbox/test/test.test/clang-linux-5.0.2/ubsan_undefined/visibility-global/test+0x40bfa6)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-10-28 21:23:45 UTC  
> Updated_at: 2018-10-28 21:24:02 UTC  
> Url: https://github.com/boostorg/gil/pull/164#issuecomment-433742751  

Indeed, Travis job `VARIANT=ubsan_undefined` also failed - https://travis-ci.org/boostorg/gil/builds/447353674 and this error  
  
```  
boost/gil/extension/toolbox/color_spaces/ycbcr.hpp:236:49:   
  runtime error: -124.402 is outside the range of representable values of type 'unsigned char'  
```  
  
is located here  
  
https://github.com/boostorg/gil/blob/32fec9f05ba8d558c7af7b1bd537c80f31c2b826/include/boost/gil/extension/toolbox/color_spaces/ycbcr.hpp#L231-L237  
  
where, AFAIU, ` (dst_channel_t)` cast is supposed to convert-and-truncate the float point value to `unsigned char`, no?  
  
However, I'd rather accept this PR and then work on this bug.

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-12-07 20:46:32 UTC  
> Updated_at: 2018-12-07 21:05:52 UTC  
> Url: https://github.com/boostorg/gil/pull/164#issuecomment-445360525  

I split parts of this PR into #176 and left this one focused on the toolbox tests fixes only in https://github.com/boostorg/gil/commit/9c2a19e01583203325912be4fabdbff313babc4d  
  
@stefanseefeld If CI builds complete as green, it is ready to merge. Please review if you have a moment.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-12-07 21:27:29 UTC  
> Url: https://github.com/boostorg/gil/pull/164#issuecomment-445370650  

@stefanseefeld Unfortunately, adding `subchroma_image.cpp` to the toolbox test sources still makes the test no longer pass - https://travis-ci.org/boostorg/gil/jobs/465128454#L1214 - that is for the same reason as discussed earlier in https://github.com/boostorg/gil/pull/164#issuecomment-433727125  
  
However, the C++ syntax has been fixed in both, `subchroma_image.cpp` and `subchroma_image.hpp`. IOW, both have been improved.  
  
So, meanwhile, I'm doing to comment out the `subchroma_image.cpp` in the toolbox test source files until we have time to look at the run-time failure of the test. This way, the improvements in this PR can be merged without breaking the CI builds.

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-12-07 22:55:03 UTC  
> Url: https://github.com/boostorg/gil/pull/164#issuecomment-445390628  

Thanks @stefanseefeld

---
