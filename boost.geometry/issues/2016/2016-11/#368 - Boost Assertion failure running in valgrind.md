# #368 - Boost Assertion failure running in valgrind [Closed]

> Username: jefesaurus  
> Created at: 2016-11-22 00:38:11 UTC  
> Updated at: 2024-10-02 15:41:25 UTC  
> Closed at: 2024-10-02 15:41:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/368  

```  
TEST(TestPolygon, BoostPolyValgrind) {  
  BoostPolygon a;  
  BoostPolygon b;  
  boost::geometry::read_wkt(  
      "POLYGON((1.75 -5.5,2.75 -5.5,2.75 -6.5,1.75 -6.5,1.75 -5.5))", a);  
  boost::geometry::read_wkt(  
      "POLYGON((1.25 -5.5,2.25 -5.5,2.25 -6.5,1.25 -6.5,1.25 -5.5))", b);  
  // Boost assertion failure in this call:  
  bool intersect = boost::geometry::intersects(a, b);  
  EXPECT_EQ(intersect, true);  
}  
```  
  
Running this in valgrind e.g.  
`valgrind  out`  
  
Yields this boost assertion failure:  
```  
[==========] Running 1 test from 1 test case.  
[----------] Global test environment set-up.  
[----------] 1 test from TestPolygon  
[ RUN      ] TestPolygon.BoostPolyValgrind  
F1122 00:33:45.722594 88400 boost_assertion_failed.cc:16] BOOST_ASSERT failed in function static bool boost::geometry::detail::segment_ratio::less<double, false>::apply(const Ratio &, const Ratio &) [Type = double, IsIntegral = false, Ratio = boost::geometry::segment_ratio<double>] on line 51. Expression: rhs.denominator() != 0  
```  
  
A stack trace:  
```  
==88400==  Address 0xffefff004 is on thread 1's stack  
==88400==  in frame #6, created by google::GetStackTrace(void**, int, int) (stacktrace_libunwind-inl.h:55)  
==88400==   
*** Check failure stack trace: ***  
    @           0x4bbe0b  google::LogMessage::SendToLog()  
    @           0x4bc2d7  google::LogMessage::Flush()  
    @           0x4bf99f  google::LogMessageFatal::~LogMessageFatal()  
    @           0x4bad0a  boost::assertion_failed()  
    @           0x42cb44  boost::geometry::detail::overlay::collinear<>::apply<>()  
    @           0x42ba9e  boost::geometry::detail::overlay::get_turn_info<>::apply<>()  
    @           0x42ae88  boost::geometry::detail::get_turns::get_turns_in_sections<>::apply<>()  
    @           0x4292f4  boost::geometry::partition<>::apply<>()  
    @           0x4290c8  boost::geometry::detail::get_turns::get_turns_generic<>::apply<>()  
    @           0x4283cc  boost::geometry::detail::disjoint::disjoint_linear<>::apply()  
    @           0x41c7e8  TestPolygon_BoostPolyValgrind_Test::TestBody()  
```

---

## Comment 1

> Username: jefesaurus  
> Created at: 2016-11-22 00:40:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/368#issuecomment-262113173  

Oh an `BoostPolygon` is typedef'd as follows:  
```  
typedef boost::geometry::model::point<double, 2, boost::geometry::cs::cartesian>  
    BoostPoint;  
  
typedef boost::geometry::model::polygon<BoostPoint, true /* Clockwise */,  
                                        true /* Closed */> BoostPolygon;  
```

---

## Comment 2

> Username: raghavendrashekhawat2  
> Created at: 2020-10-30 05:00:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/368#issuecomment-719173789  

Hi. I would like to work on this issue may i ?.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-10-30 10:13:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/368#issuecomment-719465037  

@raghavendrashekhawat2 You do not need to ask for permission to start working on an issue. You're free to advertise that you'll work on it though.  
  
Simply, work on it and once you are ready, submit a pull request.

---

## Comment 4

> Username: raghavendrashekhawat2  
> Created at: 2020-10-31 04:11:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/368#issuecomment-719880554  

Great thx.

---

## Comment 5

> Username: vissarion  
> Created at: 2024-10-02 10:40:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/368#issuecomment-2388330730  

I cannot reproduce it in Boost 1.86. @barendgehrels should we close it?

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-10-02 15:19:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/368#issuecomment-2388953375  

@vissarion yes let's close it, thanks
