# #752 Color Maps [Merged]

> Username: mborland  
> Created at: 2022-01-29 16:57:30 UTC  
> Updated at: 2022-02-09 10:19:45 UTC  
> Merged at: 2022-02-09 10:19:38 UTC  
> Closed at: 2022-02-09 10:19:39 UTC  
> Url: https://github.com/boostorg/math/pull/752  

@NAThompson Here's my cut at implementing color tables without as much code duplication. I also think this is a good opportunity to start using C++20 concepts. [Compiler support](https://en.cppreference.com/w/cpp/compiler_support/20) seems pretty good.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-01-29 17:21:33 UTC  
> Updated_at: 2022-01-29 17:24:00 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1024951535  

@mborland : I like the `concepts`.   
  
Two comments:  
  
What about:  
  
```  
boost::math::tools::kindlemann  
```  
  
rather than  
  
```  
boost::math::tools::kindlemann_color_map  
```  
  
?  
  
I wonder if the latter is too verbose.  
  
Next, will we get any performance wins if we use:  
  
```  
std::array<std::array<Real, 3>, N>  
```  
  
rather than:  
  
```  
std::array<Real, N> r;  
std::array<Real, N> g;  
std::array<Real, N> b;  
```  
  
?  
  
I also wonder if we should be using the move constructor, since these color tables are generally known at compile time. I think the tables can be `static constexpr const`, which makes moving them a bit awkward . . .  
  
Is there a concept for a `RandomAccessContainer` which has elements which live in R^3?

---

## Comment 2

> Username: mborland  
> Created_at: 2022-01-29 18:50:16 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1024966349  

1) I think using the full name describes the intent better, but agree it is verbose. Since it's an object type the user will only have to type that full thing once.  
2) I don't think in this case we can gain anything because we can't convert this into a compressed matrix (that I know of at least)  
3) Agreed this should be constexpr so now the entire class is. The test file validates use in constexpr contexts.  
4) Not out of the box. You can write your own concepts which as we move forward will need to be done (e.g. to support boost.multiprecision types).

---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-01-29 19:06:31 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1024968978  

>  I think using the full name describes the intent better, but agree it is verbose. Since it's an object type the user will only have to type that full thing once.  
  
So you're thinking the users will do:  
  
```  
auto kindlemann = boost::math::tools::kindlemann_color_map()  
```  
  
and the less verbose syntax will occur in user code, rather than in ours?  
  
> I don't think in this case we can gain anything because we can't convert this into a compressed matrix (that I know of at least)  
  
Well, you might get gains from having the referenced elements being contiguous in memory, but of course it won't change any fundamental complexity . . .  
  
  
  
... If these are all `constexpr` do they need to be classes? I feel like we may be able to get them to be straight function calls.

---

## Comment 4

> Username: mborland  
> Created_at: 2022-01-29 19:18:20 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1024970935  

>   
> So you're thinking the users will do:  
>   
> ```  
> auto kindlemann = boost::math::tools::kindlemann_color_map()  
> ```  
>   
> and the less verbose syntax will occur in user code, rather than in ours?  
>   
  
Yes, then all the user has to do is call `kindlemann(scalar)`   
  
> > I don't think in this case we can gain anything because we can't convert this into a compressed matrix (that I know of at least)  
>   
> Well, you might get gains from having the referenced elements being contiguous in memory, but of course it won't change any fundamental complexity . . .  
>   
> ... If these are all `constexpr` do they need to be classes? I feel like we may be able to get them to be straight function calls.  
  
To me derived classes was the easiest way to eliminate code repetition. It also allowed for an easy implementation of `custom_color_map` where the user can define whatever values they want.   
  
You mentioned using boost.GIL for an example. Do you have one written already I could edit to make this work? I have never used GIL before.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2022-01-29 19:36:20 UTC  
> Updated_at: 2022-01-29 19:41:31 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1024973625  

> You mentioned using boost.GIL for an example. Do you have one written already I could edit to make this work? I have never used GIL before.  
  
Actually I've never used it either. We can even write a .pnm to a textfile and then screenshot it for the docs; another option is to use lodepng; that's what I did [here](https://github.com/NAThompson/DifferentiableRaytracing/).  
  
Let me think of a 2D function which would nicely get plotted with these color maps. [This one](https://github.com/NAThompson/DifferentiableRaytracing/blob/master/figures/newton_fractal_z8_15z4_m16_viridis.png) might be good to show off viridis; the code to reproduce it is [here](https://github.com/NAThompson/DifferentiableRaytracing/blob/master/src/newton_fractal.cpp).  
  
@jzmaddock , @ckormanyos : Can you think of anything that could be displayed really nicely using a color map?

---

## Comment 6

> Username: mborland  
> Created_at: 2022-01-30 14:45:24 UTC  
> Updated_at: 2022-01-30 14:49:42 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1025158369  

@NAThompson I ended up modifying your example from DRT, and get the following output:  
  
![newton_fractal](https://user-images.githubusercontent.com/3745830/151704472-b930993f-6805-4bbd-baf6-e102ea5b6e02.png)  
  
Also [here](https://github.com/boostorg/math/pull/752/commits/5d478d660d619547fdca30fb6aad8451fb06c663#diff-e9ce09af476c80d1dcc81eea23a97e039dc9b2d19a5a22097e0fdec8e4831feeR164) is where the color table object is constructed, and [here](https://github.com/boostorg/math/pull/752/commits/5d478d660d619547fdca30fb6aad8451fb06c663#diff-e9ce09af476c80d1dcc81eea23a97e039dc9b2d19a5a22097e0fdec8e4831feeR184) is where it is used. I don't think the calls are overly verbose.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2022-01-30 16:36:19 UTC  
> Updated_at: 2022-01-30 17:50:27 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1025180746  

@mborland : That looks awesome! Looks like it wasn't too much work to get there either . . .  
  
I agree that the calls aren't too verbose, however I do wonder who will actually care to provide their own color tables. These color tables have been through extensive vetting by psychologists who measure their interpretive power. I do worry that if people *do* provide their own color tables, they'll use something like rainbow which is known to be terrible for scientific interpretation.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2022-01-30 18:19:51 UTC  
> Updated_at: 2022-01-30 18:47:21 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1025199639  

@mborland : It occurs to me that the user might want to try multiple color tables without recompilation, say, via a command line flag. Here's my first attempt:  
  
```diff  
diff --git a/example/color_maps_example.cpp b/example/color_maps_example.cpp  
index 23b3a3782..b46d84a0d 100644  
--- a/example/color_maps_example.cpp  
+++ b/example/color_maps_example.cpp  
@@ -130,9 +130,21 @@ private:  
     Real ymin_;  
 };  
   
-int main()  
+int main(int argc, char** argv)  
 {  
-    using Real = long double;  
+    using Real = double;  
+    if (argc <= 1) {  
+        std::cerr << "Usage: ./color_map {color_table}, where {color_table} is one of viridis, kindlemann, plasma, or ..\n";  
+	return 1;  
+    }  
+    std::function<std::array<Real, 3>(Real)> color_map;  
+    if (argv[1] == "viridis") {  
+        color_map = boost::math::tools::viridis_color_map<Real>;  
+    }  
+    else {  
+        std::cerr << "Could not recognize color map " << argv[1] << " options are viridis, kindlemann, plasma . . .\n";  
+	return 1;  
+    }  
     constexpr int64_t image_width = 4096;  
     constexpr int64_t image_height = 4096;  
     std::vector<uint8_t> img(4*image_width*image_height, 0);  
```  
  
but this doesn't really work . . .

---

## Review 9 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-30 18:39:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/752#pullrequestreview-867183942  

📁 include/boost/math/tools/color_maps.hpp

```diff
  23 |+ {
  24 |+ private:
  25 |+     std::array<Real, 256> r;
```

> Username: NAThompson  
> Created_at: 2022-01-30 18:39:38 UTC  
> Url: https://github.com/boostorg/math/pull/752#discussion_r795228565  

generally the convention is the member variables have the underscore, e.g. `std::array<Real, 256> r_;`


---

## Review 10 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-30 18:43:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/752#pullrequestreview-867190146  

📁 include/boost/math/tools/color_maps.hpp

```diff
  24 |+ private:
  25 |+     std::array<Real, 256> r;
  26 |+     std::array<Real, 256> g;
```

> Username: NAThompson  
> Created_at: 2022-01-30 18:43:40 UTC  
> Url: https://github.com/boostorg/math/pull/752#discussion_r795229507  

One thing I worry about with this design is that there is no fundamental reason that users should be required to choose an array of length 256. You can see I simply chose one length from the [original reference](https://www.kennethmoreland.com/color-advice/), but the reference itself specifies that there is an analytic form to many of these which indicates that it might be very reasonable for a user to specify basically any continuous function from [0,1]->[0,1]^3.. .

> Username: mborland  
> Created_at: 2022-01-30 18:59:32 UTC  
> Url: https://github.com/boostorg/math/pull/752#discussion_r795234403  

I changed the base class to take a `std::size_t` instead of constraining ourselves to 256. The `custom_color_map` can now be specified by for example `custom_color_map<double, 512>(r, g, b)`, but our provided ones are not affected.


---

## Review 11 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-30 18:45:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/752#pullrequestreview-867191565  

📁 include/boost/math/tools/color_maps.hpp

```diff
  68 |+ public:
  69 |+     constexpr plasma_color_map() :
  70 |+         detail::color_map<Real>({0.18500126283629117,0.1911189044113562,0.19712864908128114,0.2030416233571155,0.20887268107458867,0.21463072331602523,0.22032157730150803,0.2259575605729931,0.23155039796588678,0.23710641646603325,0.24262940495715968,0.24812510063567056,0.25359583439603345,0.259047386098491,0.26447987886309154,0.2698981057405734,0.2753043830681906,0.28069900692141886,0.28608641029403303,0.2914638822295278,0.29683511768078935,0.3021991776471102,0.307559383374685,0.31291664864816615,0.31826993831935324,0.3236225760753923,0.3289709965431237,0.33431870303843003,0.33966373680896456,0.3450078077262057,0.35034847407826647,0.3556874153529715,0.36102581201779066,0.3663615341003603,0.37169632394652985,0.3770276964703286,0.382357946820387,0.38768438207138256,0.3930062860759083,0.3983273716748813,0.4036425817932003,0.40895431552409317,0.41426001622424247,0.4195623952577179,0.4248563616485503,0.4301441775161412,0.4354268819265687,0.4406999402134,0.44596644643576516,0.45122228710713624,0.4564697629199718,0.4617056654598785,0.4669310023070961,0.47214475738324385,0.4773458407394621,0.48253461632433253,0.4877090328365206,0.49286905392912533,0.49801338714995985,0.5031412603622233,0.5082531828521651,0.5133462124834312,0.5184220260592395,0.5234778559822143,0.5285143492877827,0.5335306956538576,0.5385244789001884,0.5434980954707963,0.5484481027774952,0.5533750875909487,0.5582766534405778,0.5631554062153734,0.5680081900745759,0.5728333975138157,0.5776342744306816,0.5824076737546731,0.5871533984089229,0.5918701205096012,0.5965604367679973,0.6012201835310419,0.6058512183412395,0.6104521263820015,0.6150225220246975,0.6195628616073623,0.6240719801812181,0.6285513580399021,0.6329989221793823,0.6374162963015114,0.6418004809769666,0.6461536800309777,0.650476135769243,0.6547651168217998,0.6590245256639604,0.6632516551223815,0.6674477291126537,0.6716108031251352,0.6757429658756737,0.6798454057970371,0.6839159522860683,0.6879568949603344,0.6919652822236589,0.6959450042288188,0.6998941469583252,0.7038136863269572,0.7077036076142078,0.7115648684573126,0.7153974874768007,0.7192003105763611,0.7229763232162184,0.7267244865470727,0.7304437173416366,0.7341377518333918,0.7378037499188499,0.7414454244046753,0.7450587348350122,0.7486483346580772,0.7522104746641185,0.755748680542265,0.7592629017974487,0.7627520603974456,0.766216825373388,0.7696582925955131,0.773077107895631,0.7764713899964052,0.7798428195281752,0.7831920638854646,0.7865182091233281,0.7898228934175395,0.7931051428523151,0.7963656261858302,0.7996034565968984,0.8028201310987728,0.8060159189746301,0.8091884512009491,0.8123413974246995,0.8154719410234268,0.8185825533178849,0.8216705444481089,0.8247373765508464,0.8277822680188923,0.8308060259837033,0.8338091392025193,0.8367899778785102,0.8397489706421578,0.8426854482941508,0.8456008173688659,0.8484934838710892,0.8513631273192183,0.8542102862752687,0.8570334035212495,0.8598348399896373,0.8626110724885258,0.8653643579173034,0.8680923147108822,0.8707954368901418,0.8734743717670276,0.8761266641066531,0.8787535738894046,0.8813526771710705,0.8839261632148947,0.8864707162026111,0.8889878746971145,0.8914771762860599,0.8939362684731502,0.896366404995977,0.8987661885216094,0.9011348456797987,0.9034710462469783,0.9057762624345291,0.9080481396866358,0.9102863527112363,0.9124909968821041,0.9146608551755517,0.9167960662705978,0.9188933135427245,0.9209542277904884,0.9229781644169556,0.9249630587811338,0.9269098852966564,0.928815526839391,0.9306829366142825,0.9325070229302802,0.9342897265833519,0.9360290542859102,0.9377243972740386,0.9393770933973468,0.9409842661949558,0.9425456696997273,0.9440594310662802,0.9455271344382453,0.9469451155697922,0.9483136189409812,0.9496329509923357,0.9509013387619204,0.9521193680507284,0.9532833326102016,0.9543957893212712,0.9554530885899511,0.9564564274893136,0.9574039803720095,0.9582930778369493,0.9591252451658657,0.9598979045875052,0.9606124721276129,0.9612663555645452,0.9618587615061226,0.9623887555724971,0.9628559105126825,0.9632594414845331,0.9635970174364671,0.9638686962955997,0.964073330664618,0.9642088982457305,0.9642762050566095,0.9642722292530789,0.9641978734579372,0.9640500241140576,0.9638285003224122,0.9635333683894992,0.9631614522931872,0.962712538770477,0.9621866932888067,0.9615816256657105,0.960894345402275,0.9601234461397462,0.959270237581007,0.958333448735586,0.9573126236007495,0.9562062303425974,0.9550135580216242,0.9537297610630108,0.9523531724953679,0.9508874276947369,0.9493337129963176,0.9476874510448859,0.9459406787196255,0.944103300221897,0.9421780061800054,0.9401426587007371,0.9380209885848168,0.9358000817994506,0.9334816723713976,0.9310720730839354,0.9285606186650813,0.9259594465149981,0.92327053647064,0.9204816890380406,0.9176163146663037,0.9146765909646813,0.9116700174610778,0.9086068264551928,0.9055122790291467,0.9024220298268155,0.8994080090565665,0.8965597700566809,0.894058310302958},
```

> Username: NAThompson  
> Created_at: 2022-01-30 18:45:44 UTC  
> Updated_at: 2022-01-30 18:45:45 UTC  
> Url: https://github.com/boostorg/math/pull/752#discussion_r795230267  

Now that I think of it, I doubt we're even coming close to achieving double precision resolution. Would it make sense to replace these character by a 32 bit hexfloat representation?

> Username: mborland  
> Created_at: 2022-01-30 19:06:45 UTC  
> Updated_at: 2022-01-30 19:06:53 UTC  
> Url: https://github.com/boostorg/math/pull/752#discussion_r795235227  

We could. I don't think there are many displays in circulation that have a 32 bit color depth.


---

## Review 12 [Commented]

> Username: NAThompson  
> Created_at: 2022-01-31 16:22:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/752#pullrequestreview-868095742  

📁 include/boost/math/tools/color_maps.hpp

```diff
  17 |+ #include <boost/math/tools/assert.hpp>
  18 |+ 
  19 |+ #if __has_include("lodepng.h")
```

> Username: NAThompson  
> Created_at: 2022-01-31 16:22:10 UTC  
> Url: https://github.com/boostorg/math/pull/752#discussion_r795838003  

Actually this `__has_include` is a really nice trick . . .


---

## Comment 13

> Username: NAThompson  
> Created_at: 2022-01-31 17:56:21 UTC  
> Updated_at: 2022-01-31 18:42:28 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1026050902  

@mborland : I personally like huge images but the maintainers of the global boost package will complain if we add a 1.3MB file to the distribution. Could we make the image smaller?  
  
Also could you drop a benchmark into `reporting/performance`?

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2022-01-31 20:53:35 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1026198925  

Can the color maps be used on histogram data from fractal iterations such as Mandelbrot set?

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2022-01-31 21:02:01 UTC  
> Updated_at: 2022-01-31 21:03:06 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1026205459  

>> You mentioned using boost.GIL for an example. Do you have one written already I could edit to make this work? I have never used GIL before.  
  
> Actually I've never used it either.   
  
I have a non-published project that uses Boost.Gil in combination with a jpeg library that I made mostly to be portable as well. I can't find the origin of the original source code of my ported jpeg library any more. It seems to come from last century. I don't know if it can be redistributed.  
  
Aside from the jpeg LIB potential availability/portability problem, I ended up going from `boost::gil::rgb8_view_t` directly to jpeg via `boost::gil::jpeg_write_view`. But this does mandate a jpeg eingine (LIB) in the project.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2022-01-31 21:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1026215178  

> Can the color maps be used on histogram data from fractal iterations such as Mandelbrot set?  
  
Of course!

---

## Comment 17

> Username: mborland  
> Created_at: 2022-02-01 20:20:54 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1027251037  

@NAThompson Here is the performance data  
  
```  
Unable to determine clock rate from sysctl: hw.cpufrequency: No such file or directory  
2022-02-01T22:19:08+02:00  
Running ./color_tables_performance  
Run on (10 X 24.0833 MHz CPU s)  
CPU Caches:  
  L1 Data 64 KiB (x10)  
  L1 Instruction 128 KiB (x10)  
  L2 Unified 4096 KiB (x5)  
Load Average: 1.51, 1.51, 1.58  
-----------------------------------------------------------------------------------------------  
Benchmark                                                     Time             CPU   Iterations  
-----------------------------------------------------------------------------------------------  
color_table_benchmark<float>/64/real_time                  4144 ns         4144 ns       125506  
color_table_benchmark<float>/128/real_time                 8230 ns         8230 ns        84417  
color_table_benchmark<float>/256/real_time                16417 ns        16417 ns        42445  
color_table_benchmark<float>/512/real_time                32776 ns        32776 ns        21361  
color_table_benchmark<float>/1024/real_time               65499 ns        65499 ns        10596  
color_table_benchmark<float>/2048/real_time              130932 ns       130932 ns         5319  
color_table_benchmark<float>/4096/real_time              261722 ns       261716 ns         2671  
color_table_benchmark<float>/8192/real_time              523603 ns       523604 ns         1322  
color_table_benchmark<float>/16384/real_time            1046917 ns      1046917 ns          663  
color_table_benchmark<float>/32768/real_time            2093535 ns      2093536 ns          334  
color_table_benchmark<float>/65536/real_time            4185543 ns      4185545 ns          167  
color_table_benchmark<float>/131072/real_time           8373275 ns      8373277 ns           83  
color_table_benchmark<float>/262144/real_time          16747412 ns     16747405 ns           42  
color_table_benchmark<float>/524288/real_time          33508554 ns     33508524 ns           21  
color_table_benchmark<float>/1048576/real_time         66997783 ns     66997800 ns           10  
color_table_benchmark<float>/real_time_BigO               63.90 N         63.90 N  
color_table_benchmark<float>/real_time_RMS                    0 %             0 %  
color_table_benchmark<double>/64/real_time                 4112 ns         4112 ns       170016  
color_table_benchmark<double>/128/real_time                8185 ns         8185 ns        84702  
color_table_benchmark<double>/256/real_time               16317 ns        16317 ns        42690  
color_table_benchmark<double>/512/real_time               32595 ns        32595 ns        21467  
color_table_benchmark<double>/1024/real_time              65187 ns        65187 ns        10642  
color_table_benchmark<double>/2048/real_time             131284 ns       131265 ns         5228  
color_table_benchmark<double>/4096/real_time             267619 ns       267287 ns         2637  
color_table_benchmark<double>/8192/real_time             536032 ns       535327 ns         1286  
color_table_benchmark<double>/16384/real_time           1059829 ns      1059212 ns          646  
color_table_benchmark<double>/32768/real_time           2108699 ns      2107826 ns          327  
color_table_benchmark<double>/65536/real_time           4198880 ns      4197747 ns          166  
color_table_benchmark<double>/131072/real_time          8501609 ns      8492410 ns           83  
color_table_benchmark<double>/262144/real_time         16936271 ns     16925381 ns           42  
color_table_benchmark<double>/524288/real_time         33957917 ns     33924333 ns           21  
color_table_benchmark<double>/1048576/real_time        67226867 ns     67208000 ns           10  
color_table_benchmark<double>/real_time_BigO              64.27 N         64.24 N  
color_table_benchmark<double>/real_time_RMS                   1 %             1 %  
color_table_benchmark<long double>/64/real_time            4113 ns         4113 ns       170037  
color_table_benchmark<long double>/128/real_time           8183 ns         8183 ns        84687  
color_table_benchmark<long double>/256/real_time          16403 ns        16402 ns        42749  
color_table_benchmark<long double>/512/real_time          32592 ns        32592 ns        21458  
color_table_benchmark<long double>/1024/real_time         65143 ns        65143 ns        10656  
color_table_benchmark<long double>/2048/real_time        130240 ns       130240 ns         5349  
color_table_benchmark<long double>/4096/real_time        263375 ns       263214 ns         2681  
color_table_benchmark<long double>/8192/real_time        522375 ns       522349 ns         1329  
color_table_benchmark<long double>/16384/real_time      1041733 ns      1041734 ns          669  
color_table_benchmark<long double>/32768/real_time      2082051 ns      2082048 ns          335  
color_table_benchmark<long double>/65536/real_time      4164367 ns      4164363 ns          168  
color_table_benchmark<long double>/131072/real_time     8332090 ns      8332096 ns           83  
color_table_benchmark<long double>/262144/real_time    16670387 ns     16670262 ns           42  
color_table_benchmark<long double>/524288/real_time    33341579 ns     33341571 ns           21  
color_table_benchmark<long double>/1048576/real_time   66667208 ns     66667200 ns           10  
color_table_benchmark<long double>/real_time_BigO         63.58 N         63.58 N  
color_table_benchmark<long double>/real_time_RMS              0 %             0 %  
```

---

## Comment 18

> Username: NAThompson  
> Created_at: 2022-02-01 21:29:48 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1027306439  

@mborland : Shouldn't the call be constant time?

---

## Comment 19

> Username: mborland  
> Created_at: 2022-02-03 09:35:53 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1028786003  

@NAThompson This is now clean with CI. Any other changes, or good to merge?

---

## Comment 20

> Username: NAThompson  
> Created_at: 2022-02-03 16:31:04 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1029170027  

I still want to check if we can make the color data an array of structs, rather than three arrays, and see how it affects the performance. Gimme a few days . . .

---

## Comment 21

> Username: NAThompson  
> Created_at: 2022-02-06 18:52:45 UTC  
> Updated_at: 2022-02-07 03:00:16 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1030891720  

Ok I managed to hack up a script to get the color tables into an array of structs:  
  
```julia  
using CSV  
using DataFrames  
csv_reader = CSV.File("plasma-table-float-0256.csv")  
print("std::array<std::array<double, 3>, 256> plasma_data_ = {{")  
for row in csv_reader  
    println("{$(row.RGB_r), $(row.RGB_g), $(row.RGB_b)}, ")  
end  
print("}};")  
```  
  
@mborland : Accidently amend committed on a shared branch; my bad. Could you rerun the performance  benchmark on your machine? I'm getting:  
  
  
```  
./a.out                                                                                           color_tables ✱ ◼  
2022-02-06T10:52:23-08:00  
Running ./a.out  
Run on (4 X 2700 MHz CPU s)  
CPU Caches:  
  L1 Data 32 KiB (x2)  
  L1 Instruction 32 KiB (x2)  
  L2 Unified 256 KiB (x2)  
  L3 Unified 3072 KiB (x1)  
Load Average: 2.25, 1.68, 1.69  
------------------------------------------------------------------------  
Benchmark                              Time             CPU   Iterations  
------------------------------------------------------------------------  
color_table_benchmark<float>        2.30 ns         2.23 ns    303114574  
color_table_benchmark<double>       6.56 ns         6.39 ns    108312187  
```  
  
Using the array of structs, I can get:  
  
```  
color_table_benchmark<float>        2.02 ns         2.01 ns   1000000000  
color_table_benchmark<double>       6.07 ns         6.06 ns    693552276  
array_of_structs_bm<float>          2.02 ns         2.01 ns   1000000000  
array_of_structs_bm<double>         5.60 ns         5.59 ns    747409426  
```  
  
This is consistent with the amount of memory pressure being larger for doubles. It also makes it so we can dynamically bind a `std::function` to the color table allowing runtime selection of the map.  
  
I had to remove the `lodepng.h`, because if a user just happens to have it on their syspath but hasn't linked it (the lodepng link is super weird) it will break their compile.  
  
Nonetheless, here's an example that pumps out swatches of the color tables:  
  
```cpp  
//  (C) Copyright Nick Thompson 2021.  
//  (C) Copyright Matt Borland 2022.  
//  Use, modification and distribution are subject to the  
//  Boost Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
#include <array>  
#include <boost/math/tools/color_maps.hpp>  
#include <cmath>  
#include <complex>  
#include <cstdint>  
#include <iostream>  
#include <limits>  
#include <tuple>  
#include <vector>  
  
#if !__has_include("lodepng.h")  
#error "lodepng.h is required to run this example."  
#endif  
#include "lodepng.h"  
#include <iostream>  
#include <string>  
#include <vector>  
  
// In lodepng, the vector is expected to be row major, with the top row  
// specified first. Note that this is a bit confusing sometimes as it's more  
// natural to let y increase moving *up*.  
unsigned write_png(const std::string &filename,  
                   const std::vector<std::uint8_t> &img, std::size_t width,  
                   std::size_t height) {  
  unsigned error = lodepng::encode(filename, img, width, height,  
                                   LodePNGColorType::LCT_RGBA, 8);  
  if (error) {  
    std::cerr << "Error encoding png: " << lodepng_error_text(error) << "\n";  
  }  
  return error;  
}  
  
int main(int argc, char **argv) {  
  using Real = double;  
  using boost::math::tools::viridis;  
  using std::sqrt;  
  
  std::function<std::array<Real, 3>(Real)> color_map = viridis<Real>;  
  std::string requested_color_map = "viridis";  
  if (argc == 2) {  
    requested_color_map = std::string(argv[1]);  
    if (requested_color_map == "smooth_cool_warm") {  
      color_map = boost::math::tools::smooth_cool_warm<Real>;  
    } else if (requested_color_map == "plasma") {  
      color_map = boost::math::tools::plasma<Real>;  
    } else if (requested_color_map == "black_body") {  
      color_map = boost::math::tools::black_body<Real>;  
    } else if (requested_color_map == "inferno") {  
      color_map = boost::math::tools::inferno<Real>;  
    } else if (requested_color_map == "kindlmann") {  
      color_map = boost::math::tools::kindlmann<Real>;  
    } else if (requested_color_map == "extended_kindlmann") {  
      color_map = boost::math::tools::extended_kindlmann<Real>;  
    } else {  
      std::cerr << "Could not recognize color map " << argv[1] << ".";  
      return 1;  
    }  
  }  
  constexpr int64_t image_width = 1024;  
  constexpr int64_t image_height = 10;  
  std::vector<std::uint8_t> img(4 * image_width * image_height, 0);  
  
  for (int64_t j = 0; j < image_height; ++j) {  
    std::cout << "j = " << j << "\n";  
    for (int64_t i = 0; i < image_width; ++i) {  
      double t = i / (image_width - 1.0);  
      auto c = boost::math::tools::to_8bit_rgba(color_map(t));  
      int64_t idx = 4 * image_width * (image_height - 1 - j) + 4 * i;  
      img[idx + 0] = c[0];  
      img[idx + 1] = c[1];  
      img[idx + 2] = c[2];  
      img[idx + 3] = c[3];  
    }  
  }  
  // Requires lodepng.h  
  // See: https://github.com/lvandeve/lodepng for download and compilation  
  // instructions  
  write_png(requested_color_map + "_swatch.png", img, image_width,  
            image_height);  
}  
```

---

## Comment 22

> Username: NAThompson  
> Created_at: 2022-02-08 16:23:35 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1032807425  

@mborland : Nice diffs. I think I'll put swatches for all the color maps in the docs and then we'll be done.

---

## Comment 23

> Username: NAThompson  
> Created_at: 2022-02-09 06:05:20 UTC  
> Updated_at: 2022-02-09 06:15:21 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1033385077  

@mborland : Could you put eyes on the docs for me then squash merge once you're happy?

---

## Comment 24

> Username: mborland  
> Created_at: 2022-02-09 10:18:48 UTC  
> Url: https://github.com/boostorg/math/pull/752#issuecomment-1033596803  

Docs look good to me now so merging.

---
