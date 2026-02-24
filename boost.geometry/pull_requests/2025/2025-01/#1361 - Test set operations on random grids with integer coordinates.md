# #1361 Test set operations on random grids with integer coordinates [Merged]

> Username: tinko92  
> Created at: 2025-01-06 15:40:39 UTC  
> Updated at: 2025-03-18 13:19:16 UTC  
> Merged at: 2025-01-17 14:08:34 UTC  
> Closed at: 2025-01-17 14:08:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361  

As discussed in #1356 , this adds an overlay test from https://gist.github.com/tinko92/fad450e1058ce8ceebde66a6b86d45c1 that generates grids based on bit patterns and verifies the correctness of set operation by testing against the corresponding logical operation on the bit sets.  
  
The test is cleaned up and generalized to take width and height options from the command line. It is not intended to be run by default as part of the test suite (I understand, this is not the case for the robustness tests in general?) because it is only useful with a large iteration count and thereby long-running. It is also meant to be built with optimizations to mitigate the high time cost and prints failures to stdout in WKT with the corresponding bitset rather than triggering assertions.  
  
The design idea was to build a test that, in principle, could test every possible code path in overlay (for sufficiently large width and height). I lack the insight in the overlay details to judge whether it meets that goal. It is also meant to test only for non-numerical issues, so it intentionally only supports integer coordinates. The limitation to a rectangular grid is just for simplicity of implementation, but in principle every indexable, disjoint set of rings with corresponding test points should work.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2025-01-06 20:38:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2573885534  

Cool! I'll review it Wednesday

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 12:43:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537051520  

📁 test/robustness/overlay/areal_areal/Jamfile

```diff
  27 | exe ticket_9081 : ticket_9081.cpp ;
  28 |- 
  28 |+ exe random_integer_grids : random_integer_grids.cpp ;
```

> Username: barendgehrels  
> Created_at: 2025-01-08 12:43:56 UTC  
> Updated_at: 2025-01-08 12:43:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907128447  

Can you added it to the `CMakeLists.txt` too?


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 12:48:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537059726  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  53 |+ {
  54 |+     std::vector<box> out;
  55 |+     for (int y = settings.height - 1; y >= 0; --y)
```

> Username: barendgehrels  
> Created_at: 2025-01-08 12:48:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907133207  

Most of the code base has y from bottom to top. As done in GIS. I know bitmaps/screens are the other way round.  
Is there a specific reason you walk the other way round here?


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 12:49:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537063125  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  48 |+ };
  49 |+ 
  50 |+ constexpr int cell_width = 2;
```

> Username: barendgehrels  
> Created_at: 2025-01-08 12:49:53 UTC  
> Updated_at: 2025-01-08 12:49:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907135187  

This is used for both width and height, which is confusing.  
Can you either make two separate constants, or call it `cell_dimension` for example?


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 12:57:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537080188  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 112 |+             mp temp;
 113 |+             bg::union_(out, grid[i], temp);
 114 |+             out = temp;
```

> Username: barendgehrels  
> Created_at: 2025-01-08 12:57:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907144580  

You can move it


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 12:58:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537081896  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 115 |+         }
 116 |+     }
 117 |+     const auto b_gs = [&settings](bits const& b) { return std::make_pair(b, settings); };
```

> Username: barendgehrels  
> Created_at: 2025-01-08 12:58:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907145502  

what is `b_gs`?

> Username: barendgehrels  
> Created_at: 2025-01-09 19:49:15 UTC  
> Updated_at: 2025-01-09 19:49:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909355683  

Finally got it, `bit_gridsettings` probably.


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 12:59:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537083886  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 155 |+ template<typename BitOp, typename GeoOp>
 156 |+ void test_op(bits const& bits1, bits const& bits2, mp const& geo1, mp const& geo2,
 157 |+              std::string op_label, BitOp bit_op, GeoOp geo_op, std::vector<point> const& points,
```

> Username: barendgehrels  
> Created_at: 2025-01-08 12:59:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907146638  

op_label should be `const&` probably


---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 13:38:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537169683  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 212 |+                 [](std::bitset<chunk_size> b1, std::bitset<chunk_size> b2) { return b2 & (~b1); },
 213 |+                 [](mp const& g1, mp const& g2) { mp g; bg::difference(g2, g1, g); return g; },
 214 |+                 points, grid, settings, all_success);
```

> Username: barendgehrels  
> Created_at: 2025-01-08 13:38:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907196935  

This (the lines above) looks very good, great idea to test it like this.


---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 13:41:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537176438  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  37 |+ namespace bg = boost::geometry;
  38 |+ 
  39 |+ using point = bg::model::d2::point_xy<int>;
```

> Username: barendgehrels  
> Created_at: 2025-01-08 13:41:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907200928  

You probably mean here: `<BOOST_GEOMETRY_DEFAULT_TEST_TYPE>`, though it's a bit long.  
It's actually a pity that it can only test integers (though you gave the rationale). I would like to have a version for `double` as well. But we can also change it later.


---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 13:44:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537183977  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  94 |+ }
  95 |+ 
  96 |+ bits geometry_to_bits(mp const& g, std::vector<point> const& tp)
```

> Username: barendgehrels  
> Created_at: 2025-01-08 13:44:30 UTC  
> Updated_at: 2025-01-08 13:44:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907205297  

The names are short and sometimes cryptic.  
I don't say I never use abbreviations myself (I do). But these ones I don't fully get all.  
`tp`, what is it?  
`b` is probably fine in this context.  
`g` is short (but at least I understand it), I would use `geometry` here or make the `mp` a `multi_polygon_t` or `mp_t` and call it `mp` then here  
`bm` (below)  
`prng` and `gen`, I would use longer names


---

## Review 11 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 13:46:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537188944  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 164 |+     // Convenience lambda to pair bits with settings to use width/height in operator<<(os, ...)
 165 |+     const auto b_gs = [&settings](bits const& b) { return std::make_pair(b, settings); };
 166 |+     if ( ! bg::is_valid(test_geo) )
```

> Username: barendgehrels  
> Created_at: 2025-01-08 13:46:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907208492  

You can get the reason (for invalidity) and output it, it is useful in these kind of programs


---

## Review 12 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 13:47:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537190347  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 167 |+     {
 168 |+         std::cout << op_label << "(\n\t " << bg::wkt(geo1) << ",\n\t " << bg::wkt(geo2) << "\n), "
 169 |+                   << "generated from" << b_gs(bits1) << "and" << b_gs(bits2) << "is invalid.\n\n";
```

> Username: barendgehrels  
> Created_at: 2025-01-08 13:47:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907209274  

Looks like some spaces are missing, do you have a sample output?

> Username: barendgehrels  
> Created_at: 2025-01-08 14:10:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907245399  

I get them now, it's fine because the `b_gs` lambda generates new lines


---

## Review 13 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 13:47:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537191391  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 171 |+     }
 172 |+     bits expected = apply_for_each(bits1, bits2, bit_op);
 173 |+     bits obtained = geometry_to_bits(test_geo, points);
```

> Username: barendgehrels  
> Created_at: 2025-01-08 13:47:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907209938  

These can be const (not really important here, but anyway)


---

## Review 14 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 14:09:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537251706  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  46 |+     int height             = 5;
  47 |+     int count              = 1;
  48 |+     prng::result_type seed = prng::default_seed;
```

> Username: barendgehrels  
> Created_at: 2025-01-08 14:09:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907244302  

Can we generate it somehow from current time?


---

## Review 15 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 14:11:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537256378  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 239 |+              "Width of grid")
 240 |+             ("height",
 241 |+              po::value<decltype(settings.height)>(&settings.height)->default_value(settings.height),
```

> Username: barendgehrels  
> Created_at: 2025-01-08 14:11:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907246772  

The decltype is elegant. But because it's an `int`, if I specify `-10` for height, I get a segmentation fault...


---

## Review 16 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 14:12:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537260366  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 252 |+             return 1;
 253 |+         }
 254 |+         if ( ! test_all(settings) )
```

> Username: barendgehrels  
> Created_at: 2025-01-08 14:12:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907249078  

About style.  
We put conditions normally in curly braces.  
It's allowed without (IIRC) if you make it one line, so `if (! test_all(settings)) return 1;`


---

## Review 17 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 14:15:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537267297  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  53 |+ std::vector<box> grid_cells(grid_settings const& settings)
  54 |+ {
  55 |+     std::vector<box> out;
```

> Username: barendgehrels  
> Created_at: 2025-01-08 14:15:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907253022  

You could reserve width*height, here and below - it's probably a bit faster when testing a lot. Though the bg operations might overshadow the gain, of course.


---

## Review 18 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-08 14:17:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2537272442  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 262 |+     {
 263 |+         std::cout << "Other exception" << '\n';
 264 |+     }
```

> Username: barendgehrels  
> Created_at: 2025-01-08 14:17:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1907256072  

I would like to see a summary of the number of tests, the number of failures, and the time spent.

> Username: vissarion  
> Created_at: 2025-01-10 11:49:10 UTC  
> Updated_at: 2025-01-10 12:08:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910267465  

+1 here. EDIT: I mean it would be interesting to see some stats for the generated polygons (validity, failures etc).


---

## Comment 19

> Username: barendgehrels  
> Created_at: 2025-01-08 14:20:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2577790068  

> It is not intended to be run by default as part of the test suite (I understand, this is not the case for the robustness tests in general?) because it is only useful with a large iteration count and thereby long-running.   
  
This is completely fine, indeed the robustness tests are to be executed manually. I do it regularly (not all of them, but some).  
  
I'll continue tonight or tomorrow - I had some remarks but basically it looks good, I'm glad with the addition.

---

## Comment 20

> Username: tinko92  
> Created_at: 2025-01-09 12:48:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2580073921  

The second commit was intentionally not squished to delineate the new changes from the already reviewed ones but I can rebase and force push if a single commit PR is prefered.

---

## Comment 21

> Username: barendgehrels  
> Created_at: 2025-01-09 19:46:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2581121431  

> The second commit was intentionally not squished to delineate the new changes from the already reviewed ones but I can rebase and force push if a single commit PR is prefered.  
  
I usually look at `compare` so for me it's fine to squash it. But this is also fine.

---

## Review 22 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-09 19:47:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2540799200  

📁 test/robustness/overlay/areal_areal/CMakeLists.txt

```diff
  16 |     star_comb
  17 |     #ticket_9081
  18 |+     #random_integer_grid
```

> Username: barendgehrels  
> Created_at: 2025-01-09 19:47:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909353585  

It should be `random_integer_grids` (with `s`) and not commented.  
  
BTW, about the name, it's now really targeting integer. Suggestion: `random_bitset_grids` instead.


---

## Review 23 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-09 19:54:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2540811186  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 280 |+     std::cout << "\niterations: " << settings.count
 281 |+               << " errors: " << failure_count
 282 |+               << " time: " << elapsed_ms/1000 << '\n';
```

> Username: barendgehrels  
> Created_at: 2025-01-09 19:54:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909360652  

suggestion: `elapsed_ms / 1000.0` to avoid `0`


---

## Review 24 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-09 19:55:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2540812815  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 284 |+     {
 285 |+         std::cout << "Failure counts by failure mode:\n";
 286 |+         for(auto const& fm : failures) std::cout << '\t' << fm.first << ": " << fm.second << '\n';
```

> Username: barendgehrels  
> Created_at: 2025-01-09 19:55:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909361704  

Cool addition, thanks!


---

## Review 25 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-09 19:57:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2540816090  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 362 |+             return 1;
 363 |+         }
 364 |+         if (varmap.count("help"))
```

> Username: barendgehrels  
> Created_at: 2025-01-09 19:57:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909363728  

Nit, the layout of the `if` statements has three variants.  
  
I would prefer this one, so: `if` `space` `(` condition `)`, which I think is the most common in our repo (though it is not always consistent)


---

## Review 26 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-09 19:58:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2540818886  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 359 |+         if(settings.height < 1 || settings.width < 1)
 360 |+         {
 361 |+             std::cout << "Invalid dimensions, height and width need to be positive.\n";
```

> Username: barendgehrels  
> Created_at: 2025-01-09 19:58:43 UTC  
> Updated_at: 2025-01-09 19:58:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909365355  

Thanks for handling my comments! I'm OK with this PR and will approve,  
but I prefer is @vissarion has a look too.


---

## Review 27 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-09 20:00:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2540821917  

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  50 |+     int count                     = 1;
  51 |+     generator_t::result_type seed = generator_t::default_seed;
  52 |+     bool verbose                  = false;
```

> Username: barendgehrels  
> Created_at: 2025-01-09 20:00:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1909367159  

We don't usually align the columns.  
It can look good, and I don't really object - but maintenance is often cumbersome, having to realign at each renaming.


---

## Review 28 [Approved]

> Username: vissarion  
> Created_at: 2025-01-10 12:06:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2542234421  

Thanks Tinko, LGTM.

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
  89 |+ std::ostream& operator<<(std::ostream& os, std::pair<bits, grid_settings> const& b_gs)
  90 |+ {
  91 |+     if(b_gs.second.verbose)
```

> Username: vissarion  
> Created_at: 2025-01-10 11:40:40 UTC  
> Updated_at: 2025-01-10 12:06:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910259200  

coding style: please leave a space after `if`

> Username: vissarion  
> Created_at: 2025-01-10 11:47:44 UTC  
> Updated_at: 2025-01-10 12:06:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910266076  

there are a few others like this in this file, please check

---

📁 test/robustness/overlay/areal_areal/random_integer_grids.cpp

```diff
 105 |+     {
 106 |+         os << '{' << b_gs.first[0].to_ullong();
 107 |+         for(size_t i = 1; i < b_gs.first.size(); ++i) os << ' ' << b_gs.first[i].to_ullong();
```

> Username: vissarion  
> Created_at: 2025-01-10 11:41:04 UTC  
> Updated_at: 2025-01-10 12:06:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910259585  

coding style: please leave a space after `for`


---

## Review 29 [Commented]

> Username: vissarion  
> Created_at: 2025-01-10 12:49:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2542365314  

📁 test/robustness/overlay/areal_areal/random_bitset_grids.cpp

```diff
 369 |+         if (! test_all(settings)) return 1;
 370 |+     }
 371 |+     catch(std::exception const& e)
```

> Username: vissarion  
> Created_at: 2025-01-10 12:49:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910335305  

please also add a space after `catch` here


---

## Review 30 [Commented]

> Username: vissarion  
> Created_at: 2025-01-10 12:50:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2542366426  

📁 test/robustness/overlay/areal_areal/random_bitset_grids.cpp

```diff
 373 |+         std::cout << "Exception " << e.what() << '\n';
 374 |+     }
 375 |+     catch(...)
```

> Username: vissarion  
> Created_at: 2025-01-10 12:50:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910335704  

..and here


---

## Review 31 [Commented]

> Username: vissarion  
> Created_at: 2025-01-10 12:50:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2542368054  

📁 test/robustness/overlay/areal_areal/random_bitset_grids.cpp

```diff
  40 |+ namespace bg = boost::geometry;
  41 |+ 
  42 |+ using point = bg::model::d2::point_xy<BOOST_GEOMETRY_DEFAULT_TEST_TYPE>;
```

> Username: vissarion  
> Created_at: 2025-01-10 12:50:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#discussion_r1910336570  

please remove column align from here too


---

## Comment 32

> Username: tinko92  
> Created_at: 2025-01-10 13:00:28 UTC  
> Updated_at: 2025-01-10 14:54:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2582658970  

Thanks for the comments, @vissarion (I think some of the early ones maybe overlapped with my previous push) and @barendgehrels !  
  
(Sorry about that 50c20ad push. I wasn't aware of that cmake test running on my private fork, or I would have run it locally to catch that before pushing).

---

## Review 33 [Approved]

> Username: barendgehrels  
> Created_at: 2025-01-10 17:46:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1361#pullrequestreview-2543064681  

👍

---

## Comment 34

> Username: barendgehrels  
> Created_at: 2025-01-15 16:49:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2593434955  

Can we merge this?

---

## Comment 35

> Username: tinko92  
> Created_at: 2025-01-16 14:51:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1361#issuecomment-2595935818  

> Can we merge this?  
  
I checked all conversations and resolved every point as addressed. Should I merge it or is that for someone with a boostorg member role to do?

---
