# #234 - Performance issue with pixel/channel arithmetics [Open]

> Username: sdebionne  
> Created at: 2019-02-06 13:05:32 UTC  
> Updated at: 2019-10-13 15:22:37 UTC  
> Url: https://github.com/boostorg/gil/issues/234  

### Minimal Working Example (in C++)  
  
Here is a micro-benchmark written with [Google Benchmark](https://github.com/google/benchmark) that compares the performance of a simple plus operation on every pixels of an grayscale image:  
  
```c++  
#include <algorithm>  
#include <iostream>  
#include <vector>  
  
#include <benchmark/benchmark.h>  
  
#include <boost/gil.hpp>  
#include <boost/gil/extension/numeric/pixel_numeric_operations.hpp>  
  
using namespace boost::gil;  
  
static void std_algo(benchmark::State& state)  
{  
    std::vector<int16_t> img(1024 * 1024, 0);  
      
    for (auto _ : state)  
        // The code to benchmark  
        for (auto&& p : img)  
            p += 1;  
      
    if (!std::all_of(img.begin(), img.end(), [&state](auto p) { return p == state.iterations(); }))  
        state.SkipWithError("std_algo wrong result");  
}  
BENCHMARK(std_algo);  
  
static void gil_algo(benchmark::State& state)  
{  
    using namespace std::placeholders;  
    namespace gil = boost::gil;  
      
    gil::gray16_image_t img(1024, 1024, gray16_pixel_t{0});  
      
    //Pixel operation  
    auto op = std::bind(  
        gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>(),  
        _1, gray16_pixel_t{1});  
      
    for (auto _ : state)  
        // The code to benchmark  
        gil::transform_pixels(gil::const_view(img), gil::view(img), op);  
      
    if (!std::all_of(gil::view(img).begin(), gil::view(img).end(), [&state](auto p) { return p == state.iterations(); }))  
        state.SkipWithError("gil_algo wrong result");  
}  
BENCHMARK(gil_algo);  
  
BENCHMARK_MAIN();  
```  
  
### Actual behavior  
  
With MSVC, the gil version is an order of magnitude slower than the reference implementation. It looks like the metaprogramming used for unrolling of the channel arithmetic confuses the optimizer.  
  
```  
Run on (4 X 3408 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 6291K (x1)  
------------------------------------------------------  
Benchmark           Time             CPU   Iterations  
------------------------------------------------------  
std_algo        56466 ns        57199 ns        11200  
gil_algo       329919 ns       329641 ns         2133  
```  
  
On the other hand, GCC 7.3 performs much better with very little overhead.  
  
```  
Run on (4 X 3408 MHz CPU s)  
--------------------------------------------------  
Benchmark           Time           CPU Iterations  
--------------------------------------------------  
std_algo        74206 ns      74986 ns       8960  
gil_algo        81015 ns      81961 ns       8960  
```  
  
### Expected  behavior  
  
Run time should be about the same.  
  
Is there any interest in adding microbenchmark to the project? I see that there is already a [performance test suite](https://github.com/boostorg/gil/blob/develop/test/performance.cpp) but I am not sure about its coverage and how it is monitored.  
  
### Environment  
  
- Boost version 1.69  
- Compiler version: MSVC 2017  
- Build settings: /O2 /Ob2 /arch:AVX2

---

## Comment 1

> Username: mloskot  
> Created at: 2019-02-08 09:02:33 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-461735055  

I think it is a very good idea. I think benchmarks is a must-have feature in GIL.  
  
If you want to add it, I suggest you create `libs/gil/benchmark/` folder and add files there.  
  
Some loose questions:  
  
1. Do you have any idea how you would like to structure it?  
  
   One `.cpp` file per algorithm or group algorithms as in heades e.g. `channel_algorithm`, `color_base_algorithm`?  
  
   I'd suggest to keep `libs/gil/benchmark/` structure flat, with files only and file names indicating structure. (This is also how I'm hoping to restructure `libs/gil/test/`).  
   I personally like to keep benchmark `.cpp` files smaller and focused.  
  
2. You proposing to use Google Benchmark, right?  
  
   This is OK to me. It is solid framework.  
  
   I hope it can help to produce graphs. I have experience with Celero for which I developed little helper, https://github.com/mloskot/pycelerograph that outputs Bokeh graphs, e.g. https://mloskot.github.io/pycelerograph/example/report.html I will look for ways to produce similar graphs with Google Benchmark.  
  
3. How to integrate Google Benchmark with CMake?  
  
   It may be easier to integrate with our CMake config, it's probably documented already,  
   than Boost.Build, so I don't mind if it builds with CMake only first.  
  
   If you need any help in integrating with `Jamfile`-s or `CMakeLists.txt`-s for build, I will help.  
  
4. Do you expect to build benchmarks as part of CI build?  
  
   Running benchmarks on CI builds is tricky, some say it makes no sense as it is difficult to ensure comparable resources availability during subsequent builds.  
   It may be interesting experiment though to roughly compare CI builds, spot spiky regressions.  
     
   There should be at least one CI job that verifies benchmarks compile, otherwise they may stale as the `test/performance.cpp` has.  
  
-----  
  
I have no idea about status of the [test/performance.cpp](https://github.com/boostorg/gil/blob/bf67e796c6671f866d10cc4cc50d5f38caefdcad/test/performance.cpp). I have never run it myself. It may even not compile now. To resurrect it is a low-priority item on my GIL TODO list though.

---

## Comment 2

> Username: sdebionne  
> Created at: 2019-02-08 16:50:02 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-461868258  

I guess we could have two types of benchmark:  
  
 - benchmarks to prevent performance regression (pure GIL benchmarks)  
 - benchmarks to compare to reference implementations (baseline, opencv)  
  
To try to answer your questions:  
  
1. Do you have any idea how you would like to structure it?    
    
    Keeping a flat structure works for me. Maybe two folders though, one for gil's benchmarks and one for compareason with other implementations?    
  
2. You proposing to use Google Benchmark, right?  
    
    I picked Google Benchmark but really, at this point, it could be an other tool. If you think Celero is more appropriate, why not. Google Benchmark can output json and csv. There is BokehJS for online interactive visu.  
  
3. How to integrate Google Benchmark with CMake?  
  
    Do you think that Google Benchmark should be (optionally) build as part of the library or separately?  
   
    How about something like this:  
  
```   
option(GIL_BUILD_BENCHMARKS "Build benchmarks" ON)  
  
# Benchmarks  
if(GIL_BUILD_BENCHMARKS)  
    find_package(benchmark REQUIRED)  
    add_subdirectory(benchmark)  
endif()  
```  
  
```  
add_executable(color_base_algorithm  
    color_base_algorithm.cpp  
)  
  
target_link_libraries(color_base_algorithm  
    benchmark::benchmark  
    Boost::boost  
)  
```  
  
4. Do you expect to build benchmarks as part of CI build?  
  
    I agree that benchmarking in the cloud is not really reliable. But if the benchmarks are run locally, we need a way to collect the result...

---

## Comment 3

> Username: mloskot  
> Created at: 2019-02-10 11:04:15 UTC  
> Updated at: 2019-02-10 11:06:46 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462122916  

> Maybe two folders though, one for gil's benchmarks and one for compareason with other implementations?  
  
Fine.  
  
Though, I'd be careful in including comparative benchmarks.  
Boost.Geometry used to do that and it led to heated discussions, e.g. during the library review,  
that led to moving it out of Boost completely, to OSGeo Foundation repositories  
[foss4g/benchmarking/geometry_libraries/](https://svn.osgeo.org/osgeo/foss4g/benchmarking/geometry_libraries/doc/main.txt).  
  
> Google Benchmark can output json and csv  
  
Great.  
  
> Do you think that Google Benchmark should be (optionally) build as part of the library or separately?  
  
I'd prefer to rely on installed google/benchmark, simpler, I think,  
and could save CI build time.  
  
Yes, `find_package(benchmark REQUIRED)` looks good.  
  
> We need a way to collect the result...  
  
Yes, we could update documentation workflow with, roughly  
  
1. Run locally and generate Markdown + graphs  
2. Add to `libs/gil/doc` and commit  
3. Then, it will be picked in to `gh-pages` branch which @stefanseefeld set up to automatically update on regular basis.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2019-02-10 17:07:19 UTC  
> Updated at: 2019-02-10 17:08:08 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462151782  

Just adding a few thoughts here:  
  
* yes, adding benchmarks is a great idea, I agree to put them into a `benchmarks` subdirectory.  
* the choice of benchmark framework should be driven by the use-cases we want to support.  
  I added a (minimal) benchmark logic to the `Boost.uBLAS` project last summer which gives three axes to vary independently:  
    * the backend used  
    * the problem size  
    * value types  
and which allows to plot graphs to easily visualize the data (see for example https://github.com/BoostGSoC18/ublas-gpu/wiki)  
  
I believe something similar for `Boost.GIL` would be very helpful.  
The code is in https://github.com/boostorg/ublas/tree/develop/benchmarks

---

## Comment 5

> Username: mloskot  
> Created at: 2019-02-10 22:01:51 UTC  
> Updated at: 2019-02-10 23:07:29 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462179306  

I also developed some framework-less benchmarks  
* https://github.com/mloskot/spatial_index_benchmark/blob/master/spatial_index_benchmark.hpp  
* https://github.com/mloskot/json_benchmark/blob/master/json_benchmark.hpp  
  
IMO, it works well for ad-hoc benchmark, but for long-term effort with possibly increasing complexity of requirements (e.g. flexibility to pass arguments) it is better to stand on shoulders of a giant.  
Unless, one has time to waste on maintaining a bespoke framework. TBH, I'd rather spend that time contributing fixes to google/benchmark, than developing my own with close to zero chance anybody else would use.  
  
I do not fear a dependency on google/benchmark or other. I found it's less time consuming to switch between build systems, between test frameworks, etc. than fiddling with in-house framework - an unnecessary divergence from main topic: GIL.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2019-02-10 22:10:18 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462180711  

Fair enough. My main point wasn't to promote my home-grown solution, but to suggest that we clarify what we want to benchmark and compare, and what the parameter space is, before we pick a solution. If the google benchmark framework supports all the use cases we want to see covered, by all means, let's use it. (I'm usually the first one to wanting to reuse existing solutions rather than inventing my own...)

---

## Comment 7

> Username: mloskot  
> Created at: 2019-02-10 23:10:50 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462190226  

Right. Since I don't have extensive experience with google/benchmark, and @sdebionne is the main contributor here, I have to leave any detailed research to him.  
I just did not want to discourage @sdebionne before he starts :)

---

## Comment 8

> Username: sdebionne  
> Created at: 2019-02-11 09:33:12 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462264182  

Thanks for the head-up I'll have a look at what you did for uBLAS and Geometry.  
  
I am OK to keep the comparative benchmarks in a separate project to keep the discussion peaceful but, as GIL aims to provide a way to write both *generic* and *efficient* algorithms, this second allegation needs to be proved somehow. The doc talks about "generating code that is comparable in speed to that of hand-writing the algorithm for a specific image type".  
  
I see two benchmark parameters (image size, pixel types) but maybe there are more degree of freedom to consider. Google Benchmark support templated benchmarks (just like Boost.Test do support templated unit tests). TBH, I barely skimmed the functionalities offered by the framework so I will indeed need to do some research.  
  
@mloskot How about benchmarking compile time with [metabench](https://github.com/ldionne/metabench)? Could be interesting with the transition to MP11, no? I  don't think we need very thorough benchmarks, maybe benchmarking the examples would be enough?

---

## Comment 9

> Username: mloskot  
> Created at: 2019-02-11 10:27:34 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462279522  

@sdebionne Good point about comparative benchmark. I just made a comment, not objection. So, also to keep things simple, let's get all benchmarks we want into GIL. We can deal with any issues if/as they arise.  
  
The metabench is very interesting. I currently may not have time to work on it myself though.   
  
IMHO, you have freedom to choose what suits you and your plans for GIL: if you think basic timers are sufficient as @stefanseefeld mentioned, that is perfectly fine; if you choose google/benchmark, that is fine as well.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2019-02-11 15:31:38 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462370490  

Keeping an eye on compilation times for Boost.GIL is of course important, but using an actual benchmark framework for that seems overkill to me.  
@sdebionne yes, image size and pixel types are the two basic parameters we need to account for. There may be others once we add actual image-processing algorithms ([GSoC '19](https://github.com/boostorg/gil/wiki/GSoC-2019) may get us there, if we are lucky !)  
I structured the Boost.uBLAS benchmarks to make it easy to instantiate a parametrized benchmark for a range of types, and run it over a range of problem sizes. In other words, a single "benchmark run" corresponds to a benchmark instantiated for a specific set of parameter values, run over a range of sizes.   
The interesting part then consists in comparing (e.g. by plotting side-by-side) different benchmark runs, either corresponding to different parameters (e.g. to compare performance across a range of pixel types / data layouts, to figure out which ones work best for a given algorithm) or corresponding to different implementations (compare Boost.GIL to OpenCV, for example).

---

## Comment 11

> Username: mloskot  
> Created at: 2019-02-11 16:33:57 UTC  
> Updated at: 2019-02-11 16:34:33 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462395856  

Some processing could be benchmarked for variety of sizes of view (aka ROI, window, tile).  
  
I wouldn't call metabench a framework. It's just a convenient CMake module.  
  
> The interesting part then consists in comparing different benchmark runs,  
> either corresponding to different parameters or corresponding to different implementations  
  
That would be very good.  
I take very similar approach in my tiny [string_benchmark](https://github.com/mloskot/string_benchmark), with charts at http://mloskot.github.io/string_benchmark/results/

---

## Comment 12

> Username: sdebionne  
> Created at: 2019-02-12 08:48:43 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462671107  

>Some processing could be benchmarked for variety of sizes of view (aka ROI, window, tile).  
  
Right, ROI makes the algorithms run on non 1D-traversable views and that might affect performance.  
  
In the same vein, I have never been very confortable with the processing like rotation, flip that are lazy and can introduce some slow memory access pattern if they are chain with other algos (i.e. `gradient_x(rotate_90ccw(v))`). With the benchmarks, we might find some good practice on when to materialize the views (copy to a new image). Do you already have recommandations about that?

---

## Comment 13

> Username: mloskot  
> Created at: 2019-02-12 08:56:33 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462673564  

> Do you already have recommandations about that?  
  
I don't and I'm not aware of any.   
  
I'm still discovering GIL and its corners. I'm hoping to eventually come up with some sort of effective GIL cookbook. I've decided to first apply clean ups and modernisations at code level, migrate to C++11.

---

## Comment 14

> Username: stefanseefeld  
> Created at: 2019-02-12 12:36:38 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462743173  

@sdebionne I'm not quite sure I understand what you are asking about. Being able to evaluate operations lazily can be a useful optimization technique, in particular to chain / fuse operations. The decision when to use which technique can certainly be driven by gathering data through profiling or benchmarking.  
I used to work on a commercial numerical (image and signal processing) library that heavily relied on such techniques (using expression templates to fuse operations). When and where to fuse ops proved extremely difficult due to the heterogeneity of the hardware we supported, given how many choices there are (vectorizing, GPU offloading, multi-threading, etc.).  
Providing a convenient benchmarking platform to explore the parameter space is an important first step.

---

## Comment 15

> Username: sdebionne  
> Created at: 2019-02-12 13:29:25 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-462759127  

@stefanseefeld You got my question right, thanks for the thorough answer! My goal is to build a flexible processing pipeline using GIL's algorithms on views (and more specific algorithms). My question is very similar to your "when to fuse ops or not", but that is much better put!

---

## Comment 16

> Username: mloskot  
> Created at: 2019-02-20 20:47:30 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-465747572  

Some notes for future integration of Google Benchmark into GIL `Jamfile`:  
  
There's just been discussion on how to use Google Benchmark with Boost.Build, https://cpplang.slack.com/archives/C27KZLB0X/p1550694108073800, and the answer is:  
  
* If you prefer to build gbenchmark, use conan with the b2 generator, https://docs.conan.io/en/latest/reference/generators/b2.html#b2-generator, or write some more b2 script  
* If you prefer pre-build gbenchmark, use https://boostorg.github.io/build/manual/develop/index.html#bbv2.tutorial.prebuilt

---

## Comment 17

> Username: sdebionne  
> Created at: 2019-03-04 08:29:21 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-469161920  

@mloskot Thanks! I'll use the prebuilt alternative for consistency with CMake.

---

## Comment 18

> Username: simmplecoder  
> Created at: 2019-05-06 19:42:23 UTC  
> Updated at: 2019-05-06 19:45:31 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-489749263  

I've run several benchmarks searching for the cause of performance degradation. All of those are extremely confusing to me:  
  
```  
Run on (8 X 3600 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
--------------------------------------------------------------  
Benchmark                       Time           CPU Iterations  
--------------------------------------------------------------  
std_algo                    57627 ns      56250 ns      10000  
gil_algo_stdfunction       339824 ns     337672 ns       2036  
gil_algo_lambda            340944 ns     337672 ns       2036  
gil_algo_lambda_plus       341491 ns     337672 ns       2036  
gil_algo_lambda_copy       345925 ns     345346 ns       2036  
gil_algo_rangefor         4530795 ns    4464286 ns        154  
gil_algo_rangefor_plus    4555617 ns    4565747 ns        154  
gil_algo_copy_mem          469881 ns     470948 ns       1493  
```  
  
Here is the code:  
  
```  
#include <algorithm>  
#include <iostream>  
#include <vector>  
  
#include <benchmark/benchmark.h>  
  
#include <boost/gil.hpp>  
#include <boost/gil/extension/numeric/pixel_numeric_operations.hpp>  
  
using namespace boost::gil;  
  
static void std_algo(benchmark::State& state)  
{  
	std::vector<uint16_t> img(1024 * 1024, 0);  
  
	for (auto _ : state)  
		// The code to benchmark  
		for (auto&& p : img)  
			p += 1;  
  
	if (!std::all_of(img.begin(), img.end(), [&state](auto p) { return p == state.iterations(); }))  
		state.SkipWithError("std_algo wrong result");  
}  
BENCHMARK(std_algo);  
  
static void gil_algo_stdfunction(benchmark::State& state)  
{  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
  
	//Pixel operation  
	auto op = std::bind(  
		gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>(),  
		_1, gray16_pixel_t{ 1 });  
  
	for (auto _ : state)  
		// The code to benchmark  
		gil::transform_pixels(gil::const_view(img), gil::view(img), op);  
  
	if (!std::all_of(gil::view(img).begin(), gil::view(img).end(), [&state](auto p) { return p == state.iterations(); }))  
		state.SkipWithError("gil_algo_stdfunction wrong result");  
}  
BENCHMARK(gil_algo_stdfunction);  
  
static void gil_algo_lambda(benchmark::State& state)  
{  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
  
	//Pixel operation  
	auto op2 = [second_arg = gray16_pixel_t{ 1 }](gray16_pixel_t value) {  
		return gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>()(value, second_arg);  
	};  
  
	for (auto _ : state)  
		// The code to benchmark  
		gil::transform_pixels(gil::const_view(img), gil::view(img), op2);  
  
	if (!std::all_of(gil::view(img).begin(), gil::view(img).end(), [&state](auto p) { return p == state.iterations(); }))  
		state.SkipWithError("gil_algo_lambda wrong result");  
}  
BENCHMARK(gil_algo_lambda);  
  
static void gil_algo_lambda_plus(benchmark::State& state)  
{  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
  
	//Pixel operation  
	auto op2 = [second_arg = gray16_pixel_t{ 1 }](gray16_pixel_t value) {  
		return value + second_arg;  
	};  
  
	for (auto _ : state)  
		// The code to benchmark  
		gil::transform_pixels(gil::const_view(img), gil::view(img), op2);  
  
	if (!std::all_of(gil::view(img).begin(), gil::view(img).end(), [&state](auto p) { return p == state.iterations(); }))  
		state.SkipWithError("gil_algo_lambda_plus wrong result");  
}  
BENCHMARK(gil_algo_lambda_plus);  
  
static void gil_algo_lambda_copy(benchmark::State& state)  
{  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
	gil::gray16_image_t img2(1024, 1024, gray16_pixel_t{ 0 });  
  
	auto input_view = gil::const_view(img);  
	auto output_view = gil::view(img2);  
	auto plus = gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t> {};  
  
	//Pixel operation  
	auto op2 = [second_arg = gray16_pixel_t{ 1 }](gray16_pixel_t value) {  
		return gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>()(value, second_arg);  
	};  
  
	for (auto _ : state)  
		// The code to benchmark  
		gil::transform_pixels(input_view, output_view, op2);  
  
	if (!std::all_of(output_view.begin(), output_view.end(), [&state](auto p) { return p == 1; }))  
		state.SkipWithError("gil_algo_lambda_copy wrong result");  
}  
BENCHMARK(gil_algo_lambda_copy);  
  
static void gil_algo_rangefor(benchmark::State& state) {  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
	gil::gray16_image_t img2(1024, 1024, gray16_pixel_t{ 0 });  
  
	auto input_view = gil::const_view(img);  
	auto output_view = gil::view(img2);  
	auto plus = gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>{};  
  
	//Pixel operation  
	auto op2 = [second_arg = gray16_pixel_t{ 1 }](gray16_pixel_t value) {  
		return gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>()(value, second_arg);  
	};  
  
	for (auto _ : state) {  
		auto d_first = output_view.begin();  
		for (auto pixel : input_view) {  
			*d_first++ = op2(pixel);  
		}  
	}  
  
	if (!std::all_of(output_view.begin(), output_view.end(), [&state](auto p) { return p == 1; }))  
		state.SkipWithError("gil_algo_rangefor wrong result");  
}  
BENCHMARK(gil_algo_rangefor);  
  
static void gil_algo_rangefor_plus(benchmark::State& state) {  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
	gil::gray16_image_t img2(1024, 1024, gray16_pixel_t{ 0 });  
  
	auto input_view = gil::const_view(img);  
	auto output_view = gil::view(img2);  
	auto plus = gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>{};  
  
	//Pixel operation  
	auto op2 = [second_arg = gray16_pixel_t{ 1 }](gray16_pixel_t value) {  
		return gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>()(value, second_arg);  
	};  
  
	for (auto _ : state) {  
		auto d_first = output_view.begin();  
		for (const auto& pixel : input_view) {  
			*d_first++ = pixel + 1;  
		}  
	}  
  
	if (!std::all_of(output_view.begin(), output_view.end(), [&state](auto p) { return p == 1; }))  
		state.SkipWithError("gil_algo_rangefor_plus wrong result");  
}  
BENCHMARK(gil_algo_rangefor_plus);  
  
static void gil_algo_copy_mem(benchmark::State& state) {  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
  
	gil::gray16_image_t img(1024, 1024, gray16_pixel_t{ 0 });  
	gil::gray16_image_t img2(1024, 1024, gray16_pixel_t{ 0 });  
  
	auto input_view = gil::const_view(img);  
	auto output_view = gil::view(img2);  
  
	//Pixel operation  
	auto op2 = [second_arg = gray16_pixel_t{ 1 }](gray16_pixel_t value) {  
		return gil::pixel_plus_t<gray16_pixel_t, gray16_pixel_t, gray16_pixel_t>()(value, second_arg);  
	};  
  
	for (auto _ : state) {  
		auto start = std::addressof(input_view(0, 0));  
		auto end = std::addressof(input_view(input_view.width() - 1, input_view.height() - 1)) + 1;  
		auto d_start = std::addressof(output_view(0, 0));  
		while (start != end) {  
			*d_start++ = *start++ + 1;  
		}  
	}  
  
	if (!std::all_of(output_view.begin(), output_view.end(), [&state](auto p) { return p == 1; }))  
		state.SkipWithError("gil_algo_copy_mem wrong result");  
}  
BENCHMARK(gil_algo_copy_mem);  
  
BENCHMARK_MAIN();  
```  
  
From this, it seems like touching any gil related functionality causes very heavy performance penalty. Sometimes pointer based version gets to the level of gil algorithms, but never exceeds it. I'll try to run the benchmarks on mingw toolchain after setting it up.  
  
**EDIT:** fixed gil_algo_lambda appearing twice instead of gil_algo_lambda_copy being second.

---

## Comment 19

> Username: simmplecoder  
> Created at: 2019-05-06 20:29:56 UTC  
> Updated at: 2019-05-06 20:35:05 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-489764926  

I added two more benchmarks: vector of pixels that uses just range for and the other uses reinterpret cased pointer. The result strongly suggests that VC++ just chokes on any TMP going on inside GIL:  
  
```  
Run on (8 X 3600 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
-------------------------------------------------------------------------  
Benchmark                                  Time           CPU Iterations  
-------------------------------------------------------------------------  
std_algo                               57842 ns      58594 ns      11200  
gil_algo_stdfunction                  340665 ns     344292 ns       2133  
gil_algo_lambda                       339015 ns     336884 ns       1948  
gil_algo_lambda_aligned               341959 ns     337672 ns       2036  
gil_algo_lambda_plus                  339038 ns     337672 ns       2036  
gil_algo_lambda_copy                  345278 ns     345346 ns       2036  
gil_algo_rangefor                    4546372 ns    4509228 ns        149  
gil_algo_rangefor_plus               4529785 ns    4565747 ns        154  
gil_algo_copy_mem                     468043 ns     460482 ns       1493  
gil_algo_pixel_vector                 342755 ns     337672 ns       2036  
gil_algo_pixel_vector_reinterpret      57446 ns      57199 ns      11200  
```  
  
Last two benchmarks:  
  
```  
static void gil_algo_pixel_vector(benchmark::State& state) {  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
	std::vector<gil::gray16_pixel_t> img(1024*1024, gil::gray16_pixel_t(0));  
  
	for (auto _ : state) {  
		for (auto& pixel : img)  
			pixel.at(boost::mpl::int_<0>{}) = pixel.at(boost::mpl::int_<0>{}) + 1;  
	}  
  
	if (!std::all_of(img.begin(), img.end(), [&state](auto p){return p == state.iterations();}))  
		state.SkipWithError("gil_algo_pixel_vector wrong result");  
}  
BENCHMARK(gil_algo_pixel_vector);  
  
static void gil_algo_pixel_vector_reinterpret(benchmark::State& state) {  
	using namespace std::placeholders;  
	namespace gil = boost::gil;  
	std::vector<gil::gray16_pixel_t> img(1024 * 1024, gil::gray16_pixel_t(0));  
  
	for (auto _ : state) {  
		auto start = reinterpret_cast<std::uint16_t*>(img.data());  
		auto end = start + img.size();  
		for (; start != end; ++start)  
			*start += 1;  
	}  
  
	if (!std::all_of(img.begin(), img.end(), [&state](auto p) {return p == state.iterations(); }))  
		state.SkipWithError("gil_algo_pixel_vector wrong result");  
}  
BENCHMARK(gil_algo_pixel_vector_reinterpret);  
```  
  
Not sure what can be done, as this looks like optimizer bug.  
  
Compiler Version 19.16.27030.1  
Boost 1.69.1

---

## Comment 20

> Username: mloskot  
> Created at: 2019-05-06 22:02:19 UTC  
> Updated at: 2019-05-08 14:04:43 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-489799641  

@simmplecoder Thanks for the very detailed tests. I don't have any immediate conclusions to share.   
  
I'd however suggest to try the current develop branch, where Boost.MPL has been replaced with MP11.  
In fact, the develop is the required target for any new contributions. We do not maintain old versions - no bug fix releases will happen.   
  
-----  
  
@simmplecoder UPDATE:  
I've been considering to remove all uses of Boost.Iterator features (`iterator_facade` and `iterator_adaptor`) and manually implement the iterators. Perhaps this would improve performance. That, however, needs to be measured. @stefanseefeld What you think?

---

## Comment 21

> Username: mloskot  
> Created at: 2019-05-09 21:48:16 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-491080837  

@sdebionne FYI, we've started discussing the benchmarks https://lists.boost.org/boost-gil/2019/05/index.php Your insights are very welcomed!

---

## Comment 22

> Username: simmplecoder  
> Created at: 2019-05-10 20:05:15 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-491414127  

I've managed to reproduce the benchmarks on the benchmark branch that is based off of develop. The numbers don't seem to change:  
```  
Run on (8 X 3600 MHz CPU s)  
CPU Caches:  
  L1 Data 32K (x4)  
  L1 Instruction 32K (x4)  
  L2 Unified 262K (x4)  
  L3 Unified 8388K (x1)  
-------------------------------------------------------------------------  
Benchmark                                  Time           CPU Iterations  
-------------------------------------------------------------------------  
std_algo                               56186 ns      56250 ns      10000  
gil_algo_stdfunction                  330360 ns     329641 ns       2133  
gil_algo_lambda                       307160 ns     311440 ns       2358  
gil_algo_lambda_plus                  307392 ns     304813 ns       2358  
gil_algo_lambda_copy                  335386 ns     329641 ns       2133  
gil_algo_rangefor                    4723144 ns    4741379 ns        145  
gil_algo_rangefor_plus               4701030 ns    4614094 ns        149  
gil_algo_copy_mem                     523345 ns     516183 ns       1120  
gil_algo_pixel_vector                 333988 ns     329998 ns       2036  
gil_algo_pixel_vector_reinterpret      56371 ns      57199 ns      11200  
```

---

## Comment 23

> Username: mloskot  
> Created at: 2019-10-13 15:22:36 UTC  
> Url: https://github.com/boostorg/gil/issues/234#issuecomment-541427924  

Here is another micro-benchmarking library that some of us may like to consider and try out https://github.com/martinus/nanobench
