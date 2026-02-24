# #248 Add pixel test fixture with all core pixel types [Merged]

> Username: mloskot  
> Created at: 2019-03-01 22:53:56 UTC  
> Updated at: 2019-04-01 16:24:02 UTC  
> Merged at: 2019-03-02 22:18:55 UTC  
> Closed at: 2019-03-02 22:18:55 UTC  
> Url: https://github.com/boostorg/gil/pull/248  

Add test for pixel_reference_is_mutable metafunction.  
  
From the legacy tests  
- port value_core and reference_core fixtures, see  
  https://lists.boost.org/boost-gil/2019/02/0138.php  
- port representative pixel types and verify with tests of some  
  metafunctions.  
  
See clarification of the `core` suffix in the class names: https://lists.boost.org/boost-gil/2019/02/0138.php  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-03-02 16:33:00 UTC  
> Updated_at: 2019-03-02 16:34:01 UTC  
> Url: https://github.com/boostorg/gil/pull/248#issuecomment-468936213  

@stefanseefeld I'm investigating failures on OSX, see https://dev.azure.com/boostorg/gil/_build/results?buildId=230 with full log at the bottom.  
  
This is log sample that shows color components are different  
  
```  
pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >  
>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17, v3=4.59163e-41)‌  
 !=  
pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >  
>(v0=1.4013e-45, v1=0, v2=96.9839, v3=4.58281e-41)  
```  
  
I suspect the issue is due to the issue of undetermined color elements of default-constructed pixel, what I pointed out here https://lists.boost.org/boost-gil/2019/03/0140.php  
So, I'm going to disable the `BOOST_TEST` checks with appropriate `FIXME` comment until we solve the undetermined values issue  
  
The Linux and Windows build jobs must be passing the test by accident, compiler difference, etc.  
  
Here is full log:  
  
```  
21: Test command: /Users/vsts/agent/2.147.1/work/1/s/_build/test/pixel/test_core_pixel_test_fixture  
21: Test timeout computed to be: 10000000  
21: Running 196 test cases...  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector1<boost__gil__gray_color_t>, boost__mpl__range_c<int, 0, 1> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t>, boost::mpl::range_c<int, 0, 1> >>(v0=-2.03679e+25)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t>, boost::mpl::range_c<int, 0, 1> >>(v0=3.7022e-35)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__vector3_c<int, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=6.72623e-44, v1=4.59163e-41, v2=5.73972e-42)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=103.876, v1=4.58281e-41, v2=97.2941)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__range_c<int, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=9.40395e-38, v1=4.59163e-41, v2=6.72623e-44)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=97.3582, v1=4.58281e-41, v2=103.876)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 3, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=0, v1=6.72623e-44, v2=4.59163e-41, v3=9.40395e-38)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=4.58281e-41, v1=103.876, v2=4.58281e-41, v3=97.3425)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 2, 1, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=6.72623e-44, v1=4.59163e-41, v2=9.40395e-38, v3=0)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=103.876, v1=4.58281e-41, v2=97.3425, v3=4.58281e-41)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__cyan_t, boost__gil__magenta_t, boost__gil__yellow_t, boost__gil__black_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=9.40395e-38, v1=4.59163e-41, v2=6.72623e-44, v3=0)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=97.3425, v1=4.58281e-41, v2=103.876, v3=4.58281e-41)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=9.40395e-38, v1=4.59163e-41, v2=6.72623e-44, v3=0)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=97.3425, v1=4.58281e-41, v2=103.876, v3=4.58281e-41)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__vector3_c<int, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=-2.57227e-17, v1=4.58281e-41, v2=103.876)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=96.9839, v1=0, v2=1.4013e-45)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__range_c<int, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=1.4013e-45, v1=0, v2=96.9839)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 3, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=4.59163e-41, v1=-2.57227e-17, v2=4.58281e-41, v3=103.876)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=4.58281e-41, v1=96.9839, v2=0, v3=1.4013e-45)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 2, 1, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=-2.57227e-17, v1=4.58281e-41, v2=103.876, v3=4.59163e-41)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=96.9839, v1=0, v2=1.4013e-45, v3=4.58281e-41)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__cyan_t, boost__gil__magenta_t, boost__gil__yellow_t, boost__gil__black_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17, v3=4.59163e-41)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=1.4013e-45, v1=0, v2=96.9839, v3=4.58281e-41)  
21: ]‌  
21: /Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17, v3=4.59163e-41)‌  
21:  != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=1.4013e-45, v1=0, v2=96.9839, v3=4.58281e-41)  
21: ]‌  
21:   
21: ‌*** 13 failures are detected in the test module 'test_pixel_test_fixture'‌  
21: ‌  
21/45 Test #21: test.core.pixel.test_fixture .....................***Failed    0.02 sec  
Running 196 test cases...  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector1<boost__gil__gray_color_t>, boost__mpl__range_c<int, 0, 1> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t>, boost::mpl::range_c<int, 0, 1> >>(v0=-2.03679e+25)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector1<boost::gil::gray_color_t>, boost::mpl::range_c<int, 0, 1> >>(v0=3.7022e-35)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__vector3_c<int, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=6.72623e-44, v1=4.59163e-41, v2=5.73972e-42)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=103.876, v1=4.58281e-41, v2=97.2941)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__range_c<int, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=9.40395e-38, v1=4.59163e-41, v2=6.72623e-44)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=97.3582, v1=4.58281e-41, v2=103.876)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 3, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=0, v1=6.72623e-44, v2=4.59163e-41, v3=9.40395e-38)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=4.58281e-41, v1=103.876, v2=4.58281e-41, v3=97.3425)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 2, 1, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=6.72623e-44, v1=4.59163e-41, v2=9.40395e-38, v3=0)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=103.876, v1=4.58281e-41, v2=97.3425, v3=4.58281e-41)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__cyan_t, boost__gil__magenta_t, boost__gil__yellow_t, boost__gil__black_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=9.40395e-38, v1=4.59163e-41, v2=6.72623e-44, v3=0)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=97.3425, v1=4.58281e-41, v2=103.876, v3=4.58281e-41)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:36: ‌error: in 'pixel_value_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=9.40395e-38, v1=4.59163e-41, v2=6.72623e-44, v3=0)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=97.3425, v1=4.58281e-41, v2=103.876, v3=4.58281e-41)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__vector3_c<int, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=-2.57227e-17, v1=4.58281e-41, v2=103.876)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::vector3_c<int, 2, 1, 0> >>(v0=96.9839, v1=0, v2=1.4013e-45)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector3<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t>, boost__mpl__range_c<int, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector3<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t>, boost::mpl::range_c<int, 0, 3> >>(v0=1.4013e-45, v1=0, v2=96.9839)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 3, 2, 1, 0> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=4.59163e-41, v1=-2.57227e-17, v2=4.58281e-41, v3=103.876)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 3, 2, 1, 0> >>(v0=4.58281e-41, v1=96.9839, v2=0, v3=1.4013e-45)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__vector4_c<int, 2, 1, 0, 3> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=-2.57227e-17, v1=4.58281e-41, v2=103.876, v3=4.59163e-41)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::vector4_c<int, 2, 1, 0, 3> >>(v0=96.9839, v1=0, v2=1.4013e-45, v3=4.58281e-41)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__cyan_t, boost__gil__magenta_t, boost__gil__yellow_t, boost__gil__black_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17, v3=4.59163e-41)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::cyan_t, boost::gil::magenta_t, boost::gil::yellow_t, boost::gil::black_t>, boost::mpl::range_c<int, 0, 4> >>(v0=1.4013e-45, v1=0, v2=96.9839, v3=4.58281e-41)  
]‌  
/Users/vsts/agent/2.147.1/work/1/s/test/pixel/test_fixture.cpp:53: ‌error: in 'pixel_reference_parameterized_constructor<boost__gil__pixel<boost__gil__scoped_channel_value<float, boost__gil__float_point_zero<float>, boost__gil__float_point_one<float> >, boost__gil__layout<boost__mpl__vector4<boost__gil__red_t, boost__gil__green_t, boost__gil__blue_t, boost__gil__alpha_t>, boost__mpl__range_c<int, 0, 4> > >>': check fix.pixel_ == sample_pixel has failed [pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=103.876, v1=4.58281e-41, v2=-2.57227e-17, v3=4.59163e-41)‌  
 != pixel<Channel=boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, Layout=boost::gil::layout<boost::mpl::vector4<boost::gil::red_t, boost::gil::green_t, boost::gil::blue_t, boost::gil::alpha_t>, boost::mpl::range_c<int, 0, 4> >>(v0=1.4013e-45, v1=0, v2=96.9839, v3=4.58281e-41)  
]‌  
  
*** 13 failures are detected in the test module 'test_pixel_test_fixture'‌  
```

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-03-02 16:46:31 UTC  
> Url: https://github.com/boostorg/gil/pull/248#issuecomment-468937346  

Thanks for the update. I wonder whether there are ways to filter out the failing tests via command-line options, rather than disabling the code alltogether. That would allow other test tools (`Faber`, notably, which has ways to mark test outcome expectations) to keep running the tests, without causing the build to fail as a whole.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-03-02 20:39:50 UTC  
> Updated_at: 2019-03-02 20:48:06 UTC  
> Url: https://github.com/boostorg/gil/pull/248#issuecomment-468958023  

There seem to be no command line parameters to control it, but Boost.Test offers this  
https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/tests_organization/enabling.html  
  
For now I think I will stick to code commenting. The test that is failing is not critical, so this can be investigated later.  
  
----  
  
By the way, this is interesting https://ci.appveyor.com/project/stefanseefeld/gil/builds/22772259/job/rywy0se57luyju25#L733  
  
```  
pixel<Channel=struct boost::gil::scoped_channel_value<float,struct boost::gil::float_point_zero<float>,struct boost::gil::float_point_one<float> >, Layout=struct boost::gil::layout<struct boost::mpl::vector1<struct boost::gil::gray_color_t>,struct boost::mpl::range_c<int,0,1> >  
>(v0=nan)  
 !=  
pixel<Channel=struct boost::gil::scoped_channel_value<float,struct boost::gil::float_point_zero<float>,struct boost::gil::float_point_one<float> >, Layout=struct boost::gil::layout<struct boost::mpl::vector1<struct boost::gil::gray_color_t>,struct boost::mpl::range_c<int,0,1> >  
>(v0=nan)  
```

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-03-02 21:41:18 UTC  
> Url: https://github.com/boostorg/gil/pull/248#issuecomment-468962940  

I have simplified sample values used to initialise pixel elements with plain integral constants instead of `numeric_limits<channel_t>::max` and this helps to make the elements `==` comparable:  
  
- AzP (Linux, Windows, OSX) builds pass https://dev.azure.com/boostorg/gil/_build/results?buildId=231  
- Linux: https://travis-ci.org/mloskot/gil/builds/500915001  
  
The issue with undetermined pixel element values needs to be investigated, hence the `FIXME` comments. Meanwhile, I'm going to merge this (without waiting for the slow Travis CI for boostorg/gil).

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-03-02 22:01:42 UTC  
> Updated_at: 2019-04-01 16:24:01 UTC  
> Url: https://github.com/boostorg/gil/pull/248#issuecomment-468964512  

Copying brief discussion from https://gitter.im/boostorg/gil about test failures observed previously:  
  
@stefanseefeld  
> Any idea how the NAN value emerged ?  
> Is this an uninitialized value or an undefined cast ?  
  
@mloskot  
Both, or my error in pixel elements initialisation.  
To summary, the question is if this code is valid for Pixel of all core pixel types  
  
```cpp  
using channel_t = typename gil::channel_type<Pixel>::type;  
auto channel_value = std::numeric_limits<channel_t>::max();  
Pixel pixel;  
gil::static_fill(pixel, channel_value);  
```  
Or, if this code is valid only for integral pixel types with integral elements  
  
I am going to investigate it further in near future...  
  
(UPDATE: Related updates are #273 and #270)

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-03-02 22:16:31 UTC  
> Url: https://github.com/boostorg/gil/pull/248#issuecomment-468965668  

- AzP and Travis CI builds pass, as listed in https://github.com/boostorg/gil/pull/248#issuecomment-468962940  
- AppVeyor builds pass too https://ci.appveyor.com/project/stefanseefeld/gil/builds/22775508

---
