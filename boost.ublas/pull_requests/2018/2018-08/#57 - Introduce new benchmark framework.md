# #57 Introduce new benchmark framework. [Merged]

> Username: stefanseefeld  
> Created at: 2018-08-27 15:35:51 UTC  
> Updated at: 2018-09-28 23:23:21 UTC  
> Merged at: 2018-09-28 23:23:21 UTC  
> Closed at: 2018-09-28 23:23:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/57  

To make the benchmarks more useful, I propose the following functional requirements:  
  
* measure timing over a range of sizes, rather than for a single fixed parameter  
* split benchmarks into separate executables, to be able to collect timings per benchmark  
* allow generation of graphs by overlaying plots from different benchmarks (or benchmark runs) for easier (visual) comparison.  
* parametrize the code to more easily select specific (sets of) parameters, such as value-type, dimension ordering, etc.  
  
This PR prototypes the above, using a small sample of operations (matrix/matrix and matrix/vector products).  
Once approved, I'd like to convert the (other) existing benchmarks to follow the same pattern.

---

## Review 1 [Changes requested]

> Username: bassoy  
> Created_at: 2018-08-30 14:10:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/57#pullrequestreview-151004705  

produces compile error :weary:

📁 benchmarks/mm_prod.cpp

```diff
   1 |+ #include <boost/numeric/ublas/matrix.hpp>
   2 |+ #include <boost/program_options.hpp>
   3 |+ #include "prod.hpp"
```

> Username: bassoy  
> Created_at: 2018-08-30 13:52:20 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214039102  

I get a compilation error   
`gcc.compile.c++ ../../../../bin.v2/libs/numeric/ublas/benchmarks/gcc-7.3.0/debug/mm_prod.o  
mm_prod.cpp:3:10: fatal error: prod.hpp: Datei oder Verzeichnis nicht gefunden  
 #include "prod.hpp"  
          ^~~~~~~~~~  
compilation terminated.`


---

## Comment 2

> Username: bassoy  
> Created_at: 2018-08-30 14:12:33 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#issuecomment-417333933  

I suggest to build and apply continuous integration for the examples and benchmarks

---

## Review 3 [Approved]

> Username: bassoy  
> Created_at: 2018-08-31 08:16:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/57#pullrequestreview-151287794  

Detailed review

📁 benchmarks/benchmark.hpp

```diff
   8 |+ #include <vector>
   9 |+ 
  10 |+ class benchmark
```

> Username: bassoy  
> Created_at: 2018-08-31 07:42:28 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214268833  

Should not `benchmark` be an abstract class as each new `function_benchmark` supposed to inherit from `benchmark`, right?

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:02:03 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214781688  

Yes, but why should `benchmark` be abstract ? There are three virtual member functions. Arguably, the `void operation(long)` could be made abstract (as there is no point in deriving a subclass without implementing that). The other two (`setup()` and `teardown()` may not be needed, so a default noop implementation seems good enough).

---

📁 benchmarks/benchmark.hpp

```diff
  22 |+   virtual void teardown() {}
  23 |+   
  24 |+   void run(std::vector<long> const &sizes, unsigned times = 1)
```

> Username: bassoy  
> Created_at: 2018-08-31 07:45:47 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214269584  

- Isn't  `std::vector<long> const &sizes` forcing tensors and matrices to be square?  
- The standard value of times should be set to `10`

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:04:21 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214781860  

Agreed on `times`. As to the single `sizes` parameter: I'm merely trying to provide a unique way to iterate over *a* "size" parameter to allow to sweep across a range of "sizes". What "size" means for a specific benchmark is still up to the implementor. It could mean the size of a square matrix, or it could be something else.  
Given the abstract nature of benchmarks, I don't want to impose any specific meaning. But neither does it seem appropriate to have some benchmarks use a single "size" parameter, and some benchmarks two or even more.

---

📁 benchmarks/benchmark.hpp

```diff
  33 |+       auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(clock::now() - start);
  34 |+       teardown();
  35 |+       std::cout << s << ' ' << duration.count()/times << std::endl;
```

> Username: bassoy  
> Created_at: 2018-08-31 07:50:57 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214270620  

- Maybe we could include an `std::ostream` member variable to `benchmark` instead of forcing the results to be displayed on the standard output `std::cout`.  
- It would be very nice to also output the units (here milliseconds).

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:05:41 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214781977  

I'm not sure the ability to redirect to an arbitrary `std::ostream` would any useful functionality. Unix pipes to the rescue.  
Yes, the output (in particular: the metainfo block) should contain more info, including units.

---

📁 benchmarks/benchmark.hpp

```diff
  31 |+       for (unsigned i = 0; i != times; ++i)
  32 |+         operation(s);
  33 |+       auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(clock::now() - start);
```

> Username: bassoy  
> Created_at: 2018-08-31 07:51:59 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214270831  

Maybe we could also parameterize benchmark with respect to the time unit?

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:06:24 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214782049  

Time unit ? I doubt that's useful. Clock type ? Perhaps.

> Username: bassoy  
> Created_at: 2018-09-04 08:46:31 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214833280  

Well people might want to set the accuracy and determine the time interval std::chrono::milliseconds or std::chrono::nanoseconds.


📁 benchmarks/init.hpp

```diff
   1 |+ #include <boost/numeric/ublas/vector.hpp>
```

> Username: bassoy  
> Created_at: 2018-08-31 07:54:42 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214271440  

I prefer the initialization functions to be defined within benchmark functions, see [class prod](https://github.com/boostorg/ublas/pull/57/files#diff-7d56c289a5e0d5cc9fbc0ebfdb3d2126), instead of putting it into a init.hpp. This allows the benchmark function writers to initialize their data as they want.

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:08:17 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214782220  

Hmm, perhaps. Note that you (the benchmark implementer) are free to use the `init()` functions or not. The reason I didn't put them into the benchmark class is that it's much harder (if not impossible) to overload and template-specialize if the base template is defined in the benchmark base class itself. But I remain open to suggestions.

> Username: bassoy  
> Created_at: 2018-09-04 09:19:50 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214843931  

I think it is possible to simply put the init [function](https://github.com/boostorg/ublas/blob/f23dee7fa52fa9fbd9116dae4fb759303e7997e9/benchmarks/init.hpp#L15) into the benchmark [function](https://github.com/boostorg/ublas/blob/f23dee7fa52fa9fbd9116dae4fb759303e7997e9/benchmarks/mv_prod.cpp#L13) so that the initialization becomes private to each benchmark. Adding different types of initializations into init (used by different benchmark functions) in separate files, results in more dependencies between files and functions. It will be harder to maintain if the number of benchmarks increase. While I like free functions, in this case the modularity becomes an issue I think. One might argue that code should not be repeated (DRY) , however the initialization of tensors/matrices/vectors for benchmarks is usualy a one-liner using std::iota, std::fill or std::generate and it can be specialized very easily. No template specialization needed.

---

📁 benchmarks/init.hpp

```diff
   6 |+ 
   7 |+ template <typename T>
   8 |+ void init(ublas::vector<T> &v, unsigned long size, int max_value)
```

> Username: bassoy  
> Created_at: 2018-08-31 07:57:28 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214272110  

Please consider to parametrize `init` with all template parameters `ublas::vector<T>` or `ublas::matrix<T,L>` , i.e. the storage array. Otherwise, I cannot init and therefore benchmark `ublas::vector<T,std::vector<T,A>>`

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:08:42 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214782248  

Good point. We should do that !  
(Note that we can't specialize these functions. But we can overload them with additional argument types, such as matrices with specific dimension ordering.)


📁 benchmarks/mm_prod.cpp

```diff
  15 |+ }
  16 |+ 
  17 |+ int main(int argc, char **argv)
```

> Username: bassoy  
> Created_at: 2018-08-31 08:03:52 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214273478  

I am not sure if the benchmarks should be runtime-variable in terms of the data type and not in terms the vector, matrix and tensor size.  
  
We might need a more generic approach where we can select from one main functions to test and maybe including the type.

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:10:21 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214782418  

I'm not sure I understand what you have in mind. Can you elaborate ?

> Username: bassoy  
> Created_at: 2018-09-04 09:23:28 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214845071  

As the end-user is only interested in the results of the benchmarks, only contributers are interested in adding benchmarks. I prefer all benchmarks to be called from one main, similar to unit-tests. See my gist [example](https://gist.github.com/bassoy/96b03bd9204758a3f4763ee8a212b3c1).

> Username: stefanseefeld  
> Created_at: 2018-09-28 20:19:22 UTC  
> Updated_at: 2018-09-28 20:19:23 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r221370435  

Let me come back to this (sorry for the high latency):  
I disagree about the distinction between "contributors" and "end-users". I expect benchmarks to be of most value during development, where someone may want to run (and compare !) a specific set of benchmarks.  
And to support that, I need benchmarks to be runnable individually, not en bloc.  
(In fact, I'd argue the same way for tests: it's good to have a way to run an entire test suite with a single command, but ultimately it must be possible to invoke individual tests, too, especially if they fail and you want to investigate why.)


📁 benchmarks/mv_prod.cpp

```diff
  17 |+ }
  18 |+ 
  19 |+ int main(int argc, char **argv)
```

> Username: bassoy  
> Created_at: 2018-08-31 08:06:06 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214274015  

I think there are too many of the same code lines are in [mm_prod](https://github.com/boostorg/ublas/pull/57/files#diff-25c518be5ef2d6f19157cebf394e276f). Do we then have to repeat all the lines for all new benchmarks? Lets try to stick to DRY.

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:11:36 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214782517  

Please elaborate.

> Username: bassoy  
> Created_at: 2018-09-04 09:29:31 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214847087  

The main of mm_prod and mv_prod are almost the same. I thought that we can do better if the code is not repeated (DRY). See my gist [example](https://gist.github.com/bassoy/96b03bd9204758a3f4763ee8a212b3c1).


📁 benchmarks/prod.hpp

```diff
   4 |+ template <typename S> class prod;
   5 |+ 
   6 |+ template <typename R, typename O1, typename O2>
```

> Username: bassoy  
> Created_at: 2018-08-31 08:14:39 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214276084  

I would like to see different `class prod` classes for each function (matrix-matrix, matrix-vector, vector-matrix) to be benchmarked because of the initialization of the data structures.

> Username: stefanseefeld  
> Created_at: 2018-09-04 04:13:42 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214782733  

I'd like the benchmark name to exactly match the operation name (and likewise the signature).  
Why can't you do what you have in mind (use different initialization functions) with template specializations ?

> Username: bassoy  
> Created_at: 2018-09-04 09:38:06 UTC  
> Updated_at: 2018-09-28 00:54:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r214849736  

Yes template specialization would help. But why not simplify things and put the initialization and call of the function into corresponding `mv_prod` and `mm_prod` classes and files. We would then have only two files with their corresponding initializations where the repsonsible functions are within one file and not distributed. `prod.hpp` and `init.hpp` files become obsolete then.

> Username: stefanseefeld  
> Created_at: 2018-09-28 20:21:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/57#discussion_r221370987  

I expect that a few initialization functions are going to be reused across most benchmarks. And if not, specific benchmarks may pick specific initialization functions. The two are rather independent, so I chose not to bind the init functions to the benchmark classes themselves.


---
