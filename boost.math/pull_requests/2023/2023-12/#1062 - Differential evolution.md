# #1062 Differential evolution [Merged]

> Username: NAThompson  
> Created at: 2023-12-22 20:15:48 UTC  
> Updated at: 2024-01-02 01:23:49 UTC  
> Merged at: 2024-01-02 01:09:16 UTC  
> Closed at: 2024-01-02 01:09:16 UTC  
> Url: https://github.com/boostorg/math/pull/1062  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-12-26 17:17:26 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1869674079  

@jzmaddock , @mborland : The only thing I'd like to change about this is the fact it can only take one argument type. I've resigned myself to the fact that each argument must be a container, but I would like to be able to query with (say)  
  
```  
length_args = std::vector<boost::units::meters>{....};  
length_args = std::vector<boost::units::seconds>{....};  
auto cost = cost_function(length_args, time_args);  
```  
  
This seems . . . somewhat doable.

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2023-12-27 03:27:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1062#pullrequestreview-1796719368  

📁 include/boost/math/tools/differential_evolution.hpp

```diff
  28 |+ template <class T>
  29 |+ concept subscriptable = requires(const T &c) { c[0]; };
  30 |+ }
```

> Username: mborland  
> Created_at: 2023-12-27 03:13:04 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436714542  

Can this be replaced with enable_if to allow for C++14?

> Username: NAThompson  
> Created_at: 2023-12-27 04:30:23 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436735777  

@mborland : It's not even required as it stands. I can just remove it.

---

📁 include/boost/math/tools/differential_evolution.hpp

```diff
 120 |+     constexpr bool has_resize = requires(ArgumentContainer& t) {
 121 |+         t.resize(std::declval<typename ArgumentContainer::size_type>());
 122 |+     };
```

> Username: mborland  
> Created_at: 2023-12-27 03:15:43 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436715235  

I don't think you truly need the requirement here.

> Username: NAThompson  
> Created_at: 2023-12-27 04:24:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436734012  

@mborland : I do have a unit test which doesn't compile without this. However, there may be other solutions.

---

📁 include/boost/math/tools/differential_evolution.hpp

```diff
 125 |+     using std::round;
 126 |+     using std::isnan;
 127 |+     using Size_t = ArgumentContainer::size_type;
```

> Username: mborland  
> Created_at: 2023-12-27 03:16:29 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436715484  

I would rename this to avoid the confusion of just differing by one capital letter. Maybe `container_size_t`?

> Username: NAThompson  
> Created_at: 2023-12-27 04:47:09 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436741072  

I realized I actually got this wrong anyway-so I just removed it.

---

📁 include/boost/math/tools/differential_evolution.hpp

```diff
 151 |+     //  - Differential Evolution: A Practical Approach to Global Optimization
 152 |+     using std::uniform_real_distribution;
 153 |+     uniform_real_distribution<Real> dis(Real(0), Real(1));
```

> Username: mborland  
> Created_at: 2023-12-27 03:17:22 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436715738  

```diff  
-    // > a Gaussian distribution may prove somewhat faster, although it  
+    std::uniform_real_distribution<Real> dis(Real(0), Real(1));  
```  
  
There should be no legal replacements for std::uniform_real_distribution

> Username: NAThompson  
> Created_at: 2023-12-27 04:25:42 UTC  
> Updated_at: 2023-12-27 04:25:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436734432  

This one? https://github.com/mpusz/mp-units/blob/6c2550ddfc633ceb1c1ef4819f7cc9ed8c83777b/src/utility/include/mp-units/random.h#L112

---

📁 include/boost/math/tools/differential_evolution.hpp

```diff
 173 |+       }
 174 |+       if (queries) {
 175 |+         #pragma omp critical
```

> Username: mborland  
> Created_at: 2023-12-27 03:22:16 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436717008  

There does not seem to be an associated parallel region?

> Username: NAThompson  
> Created_at: 2023-12-27 04:28:12 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436735191  

@mborland : Yup, you're right; fixed.

---

📁 include/boost/math/tools/differential_evolution.hpp

```diff
 245 |+     }
 246 |+ 
 247 |+     auto it = std::min_element(cost.begin(), cost.end());
```

> Username: mborland  
> Created_at: 2023-12-27 03:25:39 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436718037  

If you're committing to C++20, std::min_element can take an execution policy argument. It should be able to use the same thread pool as OMP if implemented with TBB.

> Username: NAThompson  
> Created_at: 2023-12-27 04:29:06 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436735411  

True, but the population is small-in this case the default is only 200 elements. Even if it were much larger it would be hard to justify spawning a thread for this.

---

📁 include/boost/math/tools/differential_evolution.hpp

```diff
  41 |+   using Real = BoundType::value_type;
  42 |+   differential_evolution(BoundsContainer bounds, Real F = 0.65, double crossover_ratio = 0.5, size_t NP = 200,
  43 |+                          size_t max_generations = 1000, size_t threads = std::thread::hardware_concurrency())
```

> Username: mborland  
> Created_at: 2023-12-27 03:26:42 UTC  
> Updated_at: 2023-12-27 03:27:43 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436718352  

```diff  
-class differential_evolution {  
+                         size_t max_generations = 1000, int threads = std::thread::hardware_concurrency())  
```  
  
This would warn of implicit unsigned to signed conversion.

> Username: NAThompson  
> Created_at: 2023-12-27 04:40:35 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436738701  

You think it's preferable to the type returned by `std::thread::hardware_concurrency()`?

> Username: mborland  
> Created_at: 2023-12-27 05:09:21 UTC  
> Url: https://github.com/boostorg/math/pull/1062#discussion_r1436748640  

You're right. I thought it was int but it's actually unsigned: https://en.cppreference.com/w/cpp/thread/thread/hardware_concurrency


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-12-27 11:47:37 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1870233140  

I'm not sure I know enough about the problem domain to comment in detail (except that we do need this!), but I worry somewhat about the interface to `argmin`, specifically the need to explicitly specify the `ArgumentContainer` type via the ugly `obj.template argmin<ArgumentContainer>()` syntax (one bit of C++ I've never liked!).    
  
I wonder, is it reasonable to suppose that `ArgumentContainer` is the same type as the bounds?  That's not currently possible with the current interface, but if the type were templated on `ArgumentContainer` then the bounds could be two `ArgumentContainer` values: one for the minimum of each parameter, and one for the maximum.  
  
Second question: does this need to be an object?  Which is to say, does it get reused and/or cache values?  Or should it be a free function?  I appreciate there are a lot of parameters though!  
  
On the docs front, it would be useful to have at least one usage example in the docs, rather than just pointing to the test cases.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2023-12-27 19:32:42 UTC  
> Updated_at: 2023-12-27 20:16:28 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1870574327  

@jzmaddock :  
  
> but I worry somewhat about the interface to argmin, specifically the need to explicitly specify the ArgumentContainer type via the ugly obj.template argmin<ArgumentContainer>() syntax (one bit of C++ I've never liked!).  
  
Yeah it's horrible. I think I'll do the Julia thing and just store all the params in a struct (this was also a suggestion in the [codereview](https://codereview.stackexchange.com/questions/288551/differential-evolution)), and then I'll just make it a free function.  
  
> I wonder, is it reasonable to suppose that ArgumentContainer is the same type as the bounds?  That's not currently possible with the current interface . .   
  
Is it impossible? I wonder if we could say `BoundsContainer = std::vector<std::array<Real,2>>` then implies `ArgumentContainer` is `std::vector<Real>`, or if `BoundsContainer= std::array<std::array<Real,2>, n>` then `ArgumentContainer` must be `std::array<Real, n>`. Admittedly my template-fu is not sufficient to achieve this but it does seem doable.  
  
Nonetheless, your suggestion to just provide two arrays does indeed solve the ugliness problem and is a clear win over what currently exists.  
  
Final question: Would it make sense to create a `boost/math/optimization` directory? `boost/math/tools` seems to be quite overloaded . . .

---

## Comment 5

> Username: mborland  
> Created_at: 2023-12-27 22:49:30 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1870674557  

What about both a free function that takes a structure that unpacks into one that just takes the params list like the scipy signature?

---

## Comment 6

> Username: NAThompson  
> Created_at: 2023-12-27 23:42:37 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1870695203  

@mborland : Yeah that's probably the right approach . . .

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2023-12-28 12:17:08 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1871113038  

Some good suggestions in the review there... it's possible to take the parameter structure a step further, and create a function with named arguments:  https://pdimov.github.io/blog/2020/09/07/named-parameters-in-c20/  The drawback is that the parameters have to appear in the correct order, but defaulted ones can be omitted.  
  
> Is it impossible? I wonder if we could say BoundsContainer = std::vector<std::array<Real,2>> then implies ArgumentContainer is std::vector<Real>, or if BoundsContainer= std::array<std::array<Real,2>, n> then ArgumentContainer must be std::array<Real, n>. Admittedly my template-fu is not sufficient to achieve this but it does seem doable.  
  
Yes definitely doable I think, requires a traits class with a template-type template parameter to extract the contained type:  
  
```  
template <class Container> struct rebind_container;  
  
template <template <typename...> class Container, class T1, class T2>  
struct rebind_container<Container<T1, T2>>  
{  
   using type = Container<typename T1::value_type, T2>;  
};  
```

---

## Comment 8

> Username: NAThompson  
> Created_at: 2024-01-01 06:40:23 UTC  
> Updated_at: 2024-01-01 22:58:38 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1873186494  

@mborland , @jzmaddock : Example added, class changed to free function + parameter struct, C++20 named parameters can be used by user (though it works with C++17), docs written.  
  
The only thing I'd really like to see is real-time observation of the progress in an example, but I think that's out of reach without a GUI dependency.  
  
BTW anyone know why only Windows+gcc-8 hates the Func template syntax?  
  
```  
ifferential_evolution_test.cpp: In instantiation of 'void test_rosenbrock_saddle() [with Real = double]':  
differential_evolution_test.cpp:138:34:   required from here  
differential_evolution_test.cpp:72:45: error: no matching function for call to 'differential_evolution(<unresolved overloaded function type>, boost::math::tools::differential_evolution_parameters<std::array<double, 2> >&, std::mt19937_64&)'  
   auto local_minima = differential_evolution(rosenbrock_saddle<Real>, de_params, gen);  
                       ~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from differential_evolution_test.cpp:10:  
..\..\../boost/math/tools/differential_evolution.hpp:155:19: note: candidate: 'template<class ArgumentContainer, class Func, class URBG> ArgumentContainer boost::math::tools::differential_evolution(Func, const boost::math::tools::differential_evolution_parameters<ArgumentContainer>&, URBG&, std::invoke_result_t<Func, ArgumentContainer>, std::atomic<bool>*, std::vector<std::pair<ArgumentContainer, typename std::invoke_result<Func, ArgumentContainer>::type> >*, std::atomic<typename std::invoke_result<Func, ArgumentContainer>::type>*)'  
 ArgumentContainer differential_evolution(  
                   ^~~~~~~~~~~~~~~~~~~~~~  
..\..\../boost/math/tools/differential_evolution.hpp:155:19: note:   template argument deduction/substitution failed:  
differential_evolution_test.cpp:72:45: note:   couldn't deduce template parameter 'Func'  
   auto local_minima = differential_evolution(rosenbrock_saddle<Real>, de_params, gen);  
                       ~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 9

> Username: NAThompson  
> Created_at: 2024-01-02 01:09:02 UTC  
> Url: https://github.com/boostorg/math/pull/1062#issuecomment-1873549793  

@mborland, @jzmaddock : Ain't gonna lie, I'm sick of this now! Feel free to add comments if you see anything else that needs addressing and I'll get to it in another PR.

---
