# #599 - Loss of precision during serialization/parsing plain double ? [Closed]

> Username: marc-viala  
> Created at: 2021-08-03 16:31:49 UTC  
> Updated at: 2023-06-13 14:26:07 UTC  
> Closed at: 2023-06-13 14:26:06 UTC  
> Url: https://github.com/boostorg/json/issues/599  

I'm starting to implement some serialization tools with Boost.Json but I've got some precision issues with double values. To demonstrate this "potential" issue, you will find hereafter a code snippet to reproduce it:  
  
```  
#include <boost/json.hpp>  
  
BOOST_AUTO_TEST_CASE(bjson_double_serialize_ut)  
{  
  namespace bjs = boost::json;  
  static double ref_values[] = {  
      1217.2772861138403  
    , -161.68713249779881  
    , 267.04251495962637};  
  
  std::string s;  
  
  {  
    const bjs::value jv = {  
      ref_values[0] , ref_values[1], ref_values[2], 0.0, 0.0, 0.0};  
    s = serialize(jv);  
  }  
  
  double values[3] = {};  
  {  
    const auto jv{bjs::parse(s)};  
    values[0] = jv.as_array().at(0).as_double();  
    values[1] = jv.as_array().at(1).as_double();  
    values[2] = jv.as_array().at(2).as_double();  
  }  
  
  static constexpr auto eps{std::numeric_limits<double>::epsilon()};  
  BOOST_TEST(std::abs(ref_values[0] - values[0]) <= eps);  
  BOOST_TEST(std::abs(ref_values[1] - values[1]) <= eps);  
  BOOST_TEST(std::abs(ref_values[2] - values[2]) <= eps);  
}  
```  
  
When I compiling and running this code w/ Boost 1.75 and Microsoft Visual C++ version 14.2 (VS 2019) / Windows 10, I've got the following output:  
  
> "bjson_double_serialize_ut": check std::abs(ref_values[0] - values[0]) <= eps has failed [2.2737367544323206e-13 > 2.2204460492503131e-16]  
> "bjson_double_serialize_ut": check std::abs(ref_values[2] - values[2]) <= eps has failed [5.6843418860808015e-14 > 2.2204460492503131e-16]  
  
From my point of view, I'm expecting to get a tolerance equal or inf. to my "double machine epsilon". Is this correct and how can I address it?  
  
Marc

---

## Comment 1

> Username: marc-viala  
> Created at: 2021-08-04 06:38:53 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-892404955  

After some iterations, the first version of my unit test passes w/ Boost 1.76 and fails w/ 1.75. But maybe the issue of loss of precision during serialization/parsing is not fully addressed w/ Boost 1.76 because this new unit test hereafter (more exhaustive) is failing w/ Boost 1.76:  
  
```  
BOOST_AUTO_TEST_CASE(bjson_double_serialize_ut)  
{  
  static const std::vector ref_values = {  
      -0.27006296145688152  
    , -0.42448451824686562  
    , 0.057166336253381224  
    , 1217.2772861138403  
    , -161.68713249779881  
    , 267.04251495962637  
    , -0.66615716744854903  
    , -0.80918535242172396  
    , 0.29123256908034584  
    , 2137.0241926849581  
    , -599.61476423470071  
    , 1002.9111801605201  
    , -1.4239725866123634  
    , -1.0346132515963697  
    , 0.90790798114618365  
    , 2535.2404973980229  
    , -1207.1290929173115  
    , 2028.379668845469  
    , -2.2996584528580817  
    , -0.90521880279912548  
    , 1.6449616573025234  
    , 2314.9160231072947  
    , -1836.2705293282695  
    , 3093.6759124836558  
    , -2.7781953227473752  
    , -0.54944860097807424  
    , 1.9702410871568496  
    , 1529.7366713650281  
    , -2333.8061352785221  
    , 3939.3529821428001  
    , -3.0696620243882053  
    , -0.13139419714747352  
    , 2.0689802496328444  
    , 370.91535570754445  
    , -2578.5523049665962  
    , 4359.4347976947429  
    , 2.9538186832340876  
    , 0.29606564516163103  
    , 2.0456322162820761  
    , -879.28776788268817  
    , -2510.8913760620435  
    , 4251.6098535812462  
  };  
  
  namespace bjs = boost::json;  
  
  bjs::array arr;  
  for(const auto v: ref_values)  
    arr.emplace_back(v);  
  const auto s{serialize(arr)};  
  
  std::vector<double> values{};  
  const auto jv{bjs::parse(s)};  
  for(const auto& item: jv.as_array())  
    values.push_back(item.as_double());  
  
  BOOST_REQUIRE(std::size(ref_values) == std::size(values));  
  
  static constexpr auto eps{std::numeric_limits<double>::epsilon()};  
  for(size_t i{}, size{std::size(ref_values)}; i < size; ++i) {  
    BOOST_TEST_MESSAGE("checking value #" << i);  
    BOOST_TEST(std::abs(ref_values[i] - values[i]) <= eps);  
  }  
}    
```  
Here is my output w/ Boost.Json 1.76 on a Windows box (Win10) and Visual C++ 19.16.27045:  
  
```  
F:\TFS\Dev\Program\UnitTests\Photogrammetry_ut\nview_sensor_desc_serialize_ut.cpp(82): Entering test case "bjson_double_serialize_ut"  
checking value #0  
checking value #1  
checking value #2  
checking value #3  
checking value #4  
checking value #5  
checking value #6  
checking value #7  
checking value #8  
checking value #9  
checking value #10  
checking value #11  
error: in "bjson_double_serialize_ut": check std::abs(ref_values[i] - values[i]) <= eps has failed [1.1368683772161603e-13 > 2.2204460492503131e-16]  
checking value #12  
checking value #13  
checking value #14  
checking value #15  
checking value #16  
error: in "bjson_double_serialize_ut": check std::abs(ref_values[i] - values[i]) <= eps has failed [2.2737367544323206e-13 > 2.2204460492503131e-16]  
checking value #17  
checking value #18  
checking value #19  
checking value #20  
checking value #21  
checking value #22  
checking value #23  
checking value #24  
checking value #25  
checking value #26  
checking value #27  
checking value #28  
checking value #29  
checking value #30  
checking value #31  
checking value #32  
checking value #33  
error: in "bjson_double_serialize_ut": check std::abs(ref_values[i] - values[i]) <= eps has failed [5.6843418860808015e-14 > 2.2204460492503131e-16]  
checking value #34  
error: in "bjson_double_serialize_ut": check std::abs(ref_values[i] - values[i]) <= eps has failed [4.5474735088646412e-13 > 2.2204460492503131e-16]  
checking value #35  
checking value #36  
checking value #37  
checking value #38  
checking value #39  
checking value #40  
checking value #41  
```

---

## Comment 2

> Username: accelerated  
> Created at: 2021-12-16 14:10:31 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-995851877  

I think this issue deserves further attention. We are also experiencing a small noise when parsing doubles from a json string and although the differences tend to be extremely small in the order of 10e-12 or less, it becomes a problem when trying to compare expected outputs to calculated data. We currently write various complex quantitative models, and one way of comparing the exactness of the model is by testing its output against a pre-validated set of numbers. Previously we were using `boost::property_tree` and we didn't have this issue. After switching to `boost::json` we noticed that most of our integration tests were failing because of these discrepancies. Are there any plans of addressing this? Note also that the serialization seems to be ok, only the parsing back to double seems to be lacking precision.

---

## Comment 3

> Username: accelerated  
> Created at: 2022-01-08 13:38:56 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1007993279  

Hi, would it be possible to get an time estimate on a fix for this?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-01-10 01:20:32 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1008473539  

This is a problem and it will be addressed. No time estimate yet, but hopefully within the next Boost release.

---

## Comment 5

> Username: pdimov  
> Created at: 2022-01-10 13:11:54 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1008860643  

Testing against an absolute epsilon isn't quite correct, because the precision of `double` varies depending on its magnitude.

---

## Comment 6

> Username: grisumbras  
> Created at: 2022-01-10 13:15:16 UTC  
> Updated at: 2022-01-10 13:15:44 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1008863414  

My testing shows that for these test numbers the error is within 1 ULP

---

## Comment 7

> Username: pdimov  
> Created at: 2022-01-10 13:35:31 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1008880321  

The values that fail this test are  
```  
>eps: 1002.91118016052 0x1.f574a18d2f195p+9, 1002.91118016052 0x1.f574a18d2f194p+9  
>eps: -1207.129092917311 -0x1.2dc8430ef07fep+10, -1207.129092917312 -0x1.2dc8430ef07ffp+10  
>eps: 370.9153557075444 0x1.72ea54c06c1c4p+8, 370.9153557075445 0x1.72ea54c06c1c5p+8  
>eps: -2578.552304966596 -0x1.4251ac7b771e6p+11, -2578.552304966596 -0x1.4251ac7b771e5p+11  
```  
and the error for all of these is one bit, which is the same precision rapidjson has in its normal (non-precise) mode.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-04-17 14:49:52 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1100894173  

we need the thing

---

## Comment 9

> Username: accelerated  
> Created at: 2022-07-13 14:05:17 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1183270092  

Hi, any ideas when this issue can be addressed?

---

## Comment 10

> Username: grisumbras  
> Created at: 2022-07-14 12:55:37 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1184414808  

The imprecision is one bit. We currently don't have immediate plans for "extra precise" mode.

---

## Comment 11

> Username: accelerated  
> Created at: 2022-07-14 15:06:05 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1184560521  

It's in the order of 1e-12 or less (see sample code from @marc-viala) and also based on our observations.  So I would assume more than 1 bit...and ultimately 1 bit can make quite a difference depending where it's at. However it's not about extra-precise mode vs regular mode, its about printing X and reading back Y. This prevents usage of this library for validating important data. If there's always some delta between what goes in and what comes out, it's a problem because you can no longer tell if it's boost json's fault or some error in the data. The other json boost lib doesn't have this issue, but it's not as nice to use. Hence the necessity to fix this IMO.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2022-07-14 15:26:08 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1184582024  

Yes we need to make sure that we are bitwise perfect on floating point conversions

---

## Comment 13

> Username: grisumbras  
> Created at: 2022-07-15 07:56:42 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1185281572  

Well, your assumption is wrong. It's one bit, I checked it. The issue is that the values you use exceed IEEE double's guaranteed precision (they have 17 decimal digits, whereas only 15 are guaranteed). So, first of all, I am sceptical of you actually needing that precision, because even though some numbers with 17 decimal digits can be represented with double, most can't.  
  
Second, our parsing implementation is written with the focus on this limitation of floating point numbers. In order to support numbers that have more decimal digits we need to fallback to a slower, but more tolerant number parsing function (like std::strtod). The problem is that our parser works with partial inputs, while strtod does not. This means that introducing such a fallback is not as trivial as one would think. That's why there are no immediate plans to tackle this issue, as we first need to finish with the stuff we've already been doing. But as @vinniefalco commented above, we will return to this at some point.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-07-15 16:57:55 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1185729013  

The problem again is not precision, it is round tripping

---

## Comment 15

> Username: johnlupton-msp  
> Created at: 2022-08-07 19:48:53 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1207473714  

Hi - I am not sure this is necessarily a bug related to the number of significant figures:  
Consider the following (using Boost 1.79.0 via nuget MS Visual Studio 2022) :  
  
  
```  
...  
  
std::string x = "{\"EUR\":1.185876}";  
boost::json::value y = boost::json::parse(x);  
std::cout << y.at_pointer("/EUR") << std::endl;  
std::cout << y.at_pointer("/EUR").as_double() << std::endl;  
  
...  
  
Output:  
1.185876E0  
1.18588  
```  
  
I hope this helps

---

## Comment 16

> Username: pdimov  
> Created at: 2022-08-07 22:51:29 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1207500477  

The default precision of std::cout is 6 digits, which is why you get 1.18588 printed.

---

## Comment 17

> Username: johnlupton-msp  
> Created at: 2022-08-08 07:24:01 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1207762281  

> The default precision of std::cout is 6 digits, which is why you get 1.18588 printed.  
  
Thank you - was not aware of that (on my environment its actually 5 dp not 6 for info):

---

## Comment 18

> Username: ropieur  
> Created at: 2023-03-08 10:08:25 UTC  
> Updated at: 2023-03-08 10:28:54 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1459926862  

Hi, I am facing also this issue since my migration from rapidjson to boost json. This is really blocking due to failing unit tests. Do you have a workaround to suggest meanwhile? (patching boost json source is also ok).

---

## Comment 19

> Username: grisumbras  
> Created at: 2023-03-09 09:37:04 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1461664131  

Can you give an example of a failing test, so that I can confirm that this is indeed a minfestation of this issue?

---

## Comment 20

> Username: ropieur  
> Created at: 2023-03-10 11:10:57 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1463649401  

In order to be complete, the production code was using rapidjson, while unit tests were using json_spirit.  
The migration of production code from rapidjson to boost json makes the unit tests failing.  
Migrating unit tests from json_spirit to boost json resolved unit tests.  
  
**Note about the unit test:**  
An internal object is converted to boost json and serialized into a string.  
Note also that double values are only initialized and does not result from any computations.  
Initial internal initialization (subpart): ``MobilityInfo{ std::chrono::milliseconds{11000}, 5.0, 215.5, {}, 5.5, 302.7, 85.2, 15.9, 12.3, 123.4, { } }``  
The resulting string is parsed by json_spirit which compares it with the expected result being a spirit json value initialized with the corresponding values from the initial object. Keep in mind that the mix rapidjson/json_spirit works properly.  
json_spirit initialization (subpart): ``{ "timestamp", 11000 }, { "longitude", 5.0 }, { "latitude", 215.5 }, { "altitude", json_spirit::mValue::null }, { "skewAngle", 5.5 }, { "heading", 302.7 }, { "speed", 85.2 }, { "yaw", 15.9 }, { "pitch", 12.3 }, { "roll", 123.4 }``  
  
Here follows an example of failing unit test:  
```  
- Expected: {"activeBeam":"beam1","beams":{"beam1":{"reported":"Unlocked","requ  
ested":"Unlocked"},"beam2":{"reported":"StickyUnlocked","requested":"StickyUnlo  
cked"},"beam3":{"reported":"Locked","requested":"Locked"},"beam4":{"reported":"  
Locked","requested":"Locked"}},"enforceAibs":true,"id":"00:00:00:00:00:01","mob  
ilityInfo":{"altitude":null,"heading":302.69999999999999,"latitude":215.5000000  
0000000,"longitude":5.0000000000000000,"pitch":12.300000000000001,"roll":123.40  
000000000001,"skewAngle":5.5000000000000000,"speed":85.200000000000003,"timesta  
mp":11000,"yaw":15.900000000000000},"positionTimeout":{"count":1,"timestamp":50  
00},"state":"switching","switchSuccess":{"count":3,"timestamp":10000},"switchTi  
meout":{"count":0,"timestamp":0},"switchingBeam":null,"targetBeam":"beam2"}  
- Actual  : {"activeBeam":"beam1","beams":{"beam1":{"reported":"Unlocked","requ  
ested":"Unlocked"},"beam2":{"reported":"StickyUnlocked","requested":"StickyUnlo  
cked"},"beam3":{"reported":"Locked","requested":"Locked"},"beam4":{"reported":"  
Locked","requested":"Locked"}},"enforceAibs":true,"id":"00:00:00:00:00:01","mob  
ilityInfo":{"altitude":null,"heading":302.69999999999999,"latitude":215.4999999  
9999997,"longitude":5.0000000000000000,"pitch":12.300000000000001,"roll":123.40  
000000000001,"skewAngle":5.5000000000000000,"speed":85.199999999999989,"timesta  
mp":11000,"yaw":15.899999999999999},"positionTimeout":{"count":1,"timestamp":50  
00},"state":"switching","switchSuccess":{"count":3,"timestamp":10000},"switchTi  
meout":{"count":0,"timestamp":0},"switchingBeam":null,"targetBeam":"beam2"}  
```  
You can observe small deviation for latitude, speed, yaw  
  
```  
"latitude":215.50000000000000  
vs  
"latitude":215.49999999999997  
  
"speed":85.200000000000003  
vs  
"speed":85.199999999999989  
  
"yaw":15.900000000000000  
vs  
"yaw":15.899999999999999  
```  
  
Thank you.

---

## Comment 21

> Username: grisumbras  
> Created at: 2023-03-14 10:25:08 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1467819433  

For your example I get this string:  
```  
{"timestamp":11000,"longitude":5E0,"latitude":2.155E2,"altitude":null,"skewAngle":5.5E0,"heading":3.027E2,"speed":8.52E1,"yaw":1.59E1,"pitch":1.23E1,"roll":1.234E2}  
```  
Are you _sure_ you are using Boost.JSON and not something else? Some people confuse Boost.JSON with Boost.PropertyTree.

---

## Comment 22

> Username: ropieur  
> Created at: 2023-03-14 13:17:28 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1468088102  

@grisumbras ,  
Failing unit test uses json_spirit to verify that the serialisation by boost::json is correct. This explains why you see a different output format. As mentioned above, migrating unit tests to use boost::json instead makes them passed.

---

## Comment 23

> Username: grisumbras  
> Created at: 2023-03-15 12:22:20 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1469908626  

I don't understand your issue, then.

---

## Comment 24

> Username: grisumbras  
> Created at: 2023-05-22 15:41:41 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1557452727  

So, #890 technically fixed this, but I'll keep the issue open until we are able to use a faster implementation than strtod.

---

## Comment 25

> Username: marc-viala  
> Created at: 2023-05-24 06:49:54 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1560546557  

Thanks for taking in consideration this bug and for the follow-up.

---

## Comment 26

> Username: grisumbras  
> Created at: 2023-06-13 14:26:06 UTC  
> Url: https://github.com/boostorg/json/issues/599#issuecomment-1589425142  

Now that #905 was merged, this was properly fixed.
