# #514 Replace boost::ignore_unused_variable_warning with boost::ignore_unused [Merged]

> Username: mloskot  
> Created at: 2018-10-12 09:57:21 UTC  
> Updated at: 2018-10-12 13:36:31 UTC  
> Merged at: 2018-10-12 13:27:48 UTC  
> Closed at: 2018-10-12 13:27:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/514  

The former is provided by Boost.ConceptCheck.  
The latter is an improved equivalent provided by Boost.Core.  
  
Related to #513

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2018-10-12 11:17:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/514#pullrequestreview-164198826  

📁 doc/src/examples/algorithms/create_svg_overlay.hpp

```diff
  50 |+     boost::ignore_unused(a);
  51 |+     boost::ignore_unused(b);
  52 |+     boost::ignore_unused(range);
```

> Username: awulkiew  
> Created_at: 2018-10-12 11:17:15 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224751268  

This could be:  
  
        boost::ignore_unused(filename, a, b, range);  
  
What is the intention in using one call per variable? E.g. do you think it is clearer this way?

> Username: mloskot  
> Created_at: 2018-10-12 11:30:44 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224754084  

@awulkiew Good point. I've updated the commit.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2018-10-12 12:07:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/514#pullrequestreview-164212771  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 407 |-         boost::ignore_unused_variable_warning(ip_index);
 408 |-         boost::ignore_unused_variable_warning(tp_model);
 406 |+         boost::ignore_unused(i2, j2, (ip_index, tp_model);
```

> Username: awulkiew  
> Created_at: 2018-10-12 12:07:38 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224762220  

oops.

> Username: mloskot  
> Created_at: 2018-10-12 12:18:15 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224764543  

oops, thanks!  
  
@awulkiew Possibly, I did not catch that buggy bracket because of the failing test while (Linux, GCC 5.5)  
  
```  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(0 0,1 0,2 1,3 5,4 0) and LINESTRING(1 0,2 1,3 5) -> Expected turns: ecc== tii++ tux== Detected turns: iuu++ tii++ tuu==  
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(0 0,1 0,2 1,3 5,4 0) and LINESTRING(3 5,2 1,1 0) -> Expected turns #: 3 detected turns #: 2  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(0 0,1 0,2 1,3 5,4 0) and LINESTRING(3 5,2 1,1 0) -> Expected turns: ecc== tix+= tui=+ Detected turns: iuu++ tix+=  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(1 0,2 1,3 5) and LINESTRING(0 0,1 0,2 1,3 5,4 0) -> Expected turns: ecc== tii++ txu== Detected turns: iuu++ tii++ tuu==  
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(3 5,2 1,1 0) and LINESTRING(0 0,1 0,2 1,3 5,4 0) -> Expected turns #: 3 detected turns #: 2  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(3 5,2 1,1 0) and LINESTRING(0 0,1 0,2 1,3 5,4 0) -> Expected turns: ecc== tiu+= txi=+ Detected turns: iuu++ txi=+  
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) and LINESTRING(4 5,3 2,1 0,0 0) -> Expected turns #: 3 detected turns #: 2  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) and LINESTRING(4 5,3 2,1 0,0 0) -> Expected turns: ecc== mix+= txi=+ Detected turns: ecc== mix+=  
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(4 5,3 2,1 0,0 0) and LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) -> Expected turns #: 3 detected turns #: 2  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(4 5,3 2,1 0,0 0) and LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) -> Expected turns: ecc== mxi=+ tix+= Detected turns: ecc== mxi=+  
test/algorithms/overlay/test_get_turns.hpp(214): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(6 3,0 3,0 0) and LINESTRING(0 0,0 2,0 3,0 1) -> Expected turns #: 4 detected turns #: 2  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(6 3,0 3,0 0) and LINESTRING(0 0,0 2,0 3,0 1) -> Expected turns: mux== tii+= tix+= txi=+ Detected turns: mux== txi=+  
test/algorithms/overlay/test_get_turns.hpp(243): error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(2 2,5 -1,15 2,18 0,20 0) and LINESTRING(30 0,19 0,18 0,0 0) -> Expected turns: iuu++ iuu++ mxi=+ tiu+= Detected turns: iuu++ iuu++ miu+= mxi=+  
  
*** 13 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2018-10-12 12:08:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/514#pullrequestreview-164213047  

📁 include/boost/geometry/geometries/concepts/polygon_concept.hpp

```diff
  72 |+             boost::ignore_unused(ci);
  73 |+             boost::ignore_unused(poly);
  74 |+             boost::ignore_unused(cpoly);
```

> Username: awulkiew  
> Created_at: 2018-10-12 12:08:32 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224762448  

One function call.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2018-10-12 12:08:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/514#pullrequestreview-164213080  

📁 include/boost/geometry/geometries/concepts/polygon_concept.hpp

```diff
 119 |+             boost::ignore_unused(ce);
 120 |+             boost::ignore_unused(ci);
 121 |+             boost::ignore_unused(cpoly);
```

> Username: awulkiew  
> Created_at: 2018-10-12 12:08:37 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224762472  

One function call.

> Username: mloskot  
> Created_at: 2018-10-12 12:41:08 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224769864  

I skipped those as the list of argument is quite long  
  
```  
#ifdef BOOST_NO_CXX11_VARIADIC_TEMPLATES  
            boost::ignore_unused(e, i, ce, ci, poly, cpoly);  
#else  
            boost::ignore_unused(e, i, ce, ci);  
            boost::ignore_unused(poly, cpoly);  
#endif  
```  
  
I'll replace with two calls

> Username: awulkiew  
> Created_at: 2018-10-12 13:05:10 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224776415  

Right, the emulation supports up to 5 arguments right now. So it's ok to call twice. I'd remove `#ifdef` because it needlessly complicates the code. With it it's 6 lines, the same number of lines as if you called once per variable.

> Username: mloskot  
> Created_at: 2018-10-12 13:06:24 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224776767  

Yes, it's called twice now. The PR has been updated.

> Username: awulkiew  
> Created_at: 2018-10-12 13:14:03 UTC  
> Updated_at: 2018-10-12 13:17:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224778881  

Actually, this is the last place where one function could be called. Two calls we're talking about are above.

> Username: mloskot  
> Created_at: 2018-10-12 13:17:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#discussion_r224779931  

Yes, it's fixed.


---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-10-12 13:27:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/514#issuecomment-429325323  

Thanks!

---
