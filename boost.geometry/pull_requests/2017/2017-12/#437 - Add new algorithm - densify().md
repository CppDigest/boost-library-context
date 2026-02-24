# #437 Add new algorithm - densify() [Merged]

> Username: awulkiew  
> Created at: 2017-12-04 22:03:58 UTC  
> Updated at: 2018-01-23 00:11:02 UTC  
> Merged at: 2018-01-16 13:45:07 UTC  
> Closed at: 2018-01-16 13:45:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437  

This algorithm adds points on each existing segment in order to make segments shorter than passed `max_distance`. ~I called it `complexify` but if someone has a better idea I'm open to suggestion.~ EDIT: It's called `densify()`.  
  
The parameters are input geometry, output geometry, max distance and optional strategy. For now the default strategy for geographic CS is geographic, so it's different than e.g. for relops and setops where the default strategy is spherical. We should change one or the other I guess, probably the default intersection strategy because geographic distance and area strategies are used by default for geographic geometries.  
  
Example (in- red, out- green):  
  
![aa](https://user-images.githubusercontent.com/1226951/33578154-c96f989e-d945-11e7-90c8-3362df7bb91d.png)  
  
Note that this is simple visualization in the coordinates space. This is why the red polygon above has straight edges (same as the green one). The library may treat them as geodesic segments depending on passed strategy. Because more points were added the edges looks like curved lines in case of green polygon.  
  
This function may be useful for  
- visualization/testing/debugging of geographic formulas/algorithms  
- projections (https://github.com/boostorg/geometry/pull/394) if a user wants to add more points before performing a projection to more accurately represent a geometry.

---

## Comment 1

> Username: mkaravel  
> Created_at: 2017-12-04 22:55:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-349134676  

I think that a more appropriate name would be "densify".  
  
In terms of semantics, what happens if you have two consecutive points on a segment that are *exactly* equal to the threshold?  
  
What happens when due to numerical errors you compute a number of sample points that produce distances that are slightly larger than the threshold (again due to numerical errors)?  
  
Also, again in terms of semantics, what is the guarantee that is given if any? The way you describe it there is reason not to make the segments arbitrarily small.  
  
How are the generated segments produced? From the code it seems that you are trying to evenly distribute them. Maybe this should be part of the semantics for the generated output.  
  
I have to admit that I have not really looked at the code in detail, so please point me to parts of the code that address, my questions.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2017-12-04 22:57:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-349135160  

This is a good idea, indeed necessary and I believe it is normally called Densify. At least in Esri but I have seen it more  
  
http://desktop.arcgis.com/en/arcmap/10.3/manage-data/editing-existing-features/adding-a-vertex-at-an-interval-to-densify-a-line.htm

---

## Comment 3

> Username: vissarion  
> Created_at: 2017-12-05 10:27:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-349261867  

+1 for `densify`   
  
postgis also uses that term; here: https://postgis.net/docs/ST_FrechetDistance.html used for more accurate Frechet distance computation

---

## Comment 4

> Username: awulkiew  
> Created_at: 2017-12-05 13:37:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-349305911  

@mkaravel   
  
As introduction, the algorithm traverses the geometry and for each segment calls the strategy. So how the segment is handled depends entirely on the strategy which fills the other geometry. I've implemented it like that because in geographic CS inverse and direct solutions has to be calculated during the generation of points in some way or the other and this is CS specific. Currently the algorithm is linear/iterative so inverse formula is called to get azimuth and distance and then direct formula is called N times to generate N missing points, this is done entirely in strategy. Let's call it algorithm [1]. A different approach comes to mind where e.g. segment is divided in a recursive manner by half, until the length is shorter than threshold, this would allow to design more elegant strategy since the other geometry filling part coudl be moved to the algorithm. This could also generate more accurate geographic segment representation because the closer to the second point the azimuth calculated by inverse formula would be more accurate. However in this case the strategy would be required to call both inverse and direct formula for each segment division and more segments would be generated. So my esstimate is that the algorithm would be 2x slower. Let's call this algorithm [2].  
  
> In terms of semantics, what happens if you have two consecutive points on a segment that are exactly equal to the threshold?  
  
The segment is left as it is (this is done in strategy).  
  
> What happens when due to numerical errors you compute a number of sample points that produce distances that are slightly larger than the threshold (again due to numerical errors)?  
  
The segments that are slightly too long are in the output and nothing is done with them. Such case is in the test file with a comment. And by "slightly too long" I mean a few meters for around 10km long segments generated for initial segment 2x longer with andoyer inverse formula and thomas direct 1st order. So these may be inaccuracies of formulas not even numerical errors.  
  
> Also, again in terms of semantics, what is the guarantee that is given if any? The way you describe it there is reason not to make the segments arbitrarily small.  
  
Currently there is no guarantee regarding the lengths of output segments as the behavior is defined in strategy and as you noticed due to the iterative nature of the algorithm [1]. If e.g. algorithm [2] was used then we could guarantee that a corresponding (using the same formulas) call of `length()` for each segment would return valud smaller than expected. However the formulas themselves may be not very accurate anyway so we have not guarantee that the distance "in reality" is what we expected, but that is understandable. I don't understand the second part. In case I didn't answer it above already could you clarify?  
  
>How are the generated segments produced? From the code it seems that you are trying to evenly distribute them. Maybe this should be part of the semantics for the generated output.  
  
Yes, and as described above this is defined in strategy (algorithm [1]). What do you mean by "part of the semantics for the generated output"?  
  
> I have to admit that I have not really looked at the code in detail, so please point me to parts of the code that address, my questions.  
  
The relevant parts are in strategies so `strategies/cartesian/complexify.hpp`, `strategies/spherical/complexify.hpp` and `strategies/geographic/complexify.hpp`. Each strategy takes a segment of input geometry and inserts points into a range of output geometry.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2017-12-05 13:38:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-349306172  

@mkaravel @barendgehrels @vissarion Thanks. I'll rename it to `densify`.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2018-01-10 14:20:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-356615529  

Thanks, I made some comments

---

## Comment 7

> Username: awulkiew  
> Created_at: 2018-01-10 15:22:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-356634338  

@barendgehrels I cannot see the comments.

---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2018-01-10 16:10:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/437#pullrequestreview-87842590  

Ah, sorry, had to submit the review. Here it is

📁 include/boost/geometry/strategies/cartesian/densify.hpp

```diff
  62 |+         signed_size_type n = signed_size_type(len2d / length_threshold);
  63 |+         if (n <= 0)
  64 |+             return;
```

> Username: barendgehrels  
> Created_at: 2018-01-10 13:59:45 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160683915  

Please add, here and at some other places, curly braces for return, as we are used to do

---

📁 include/boost/geometry/strategies/cartesian/densify.hpp

```diff
  60 |+         // taken into account. This probably should be changed. Also in the
  61 |+         // other strategies dimensions > 2 should be taken into account.
  62 |+         signed_size_type n = signed_size_type(len2d / length_threshold);
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:00:20 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160684053  

Maybe we should assert that length_threshold > 0, or is that done elsewhere?

> Username: awulkiew  
> Created_at: 2018-01-10 18:57:08 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160767811  

No, it should probably be an assertion in strategy and exception in algorithm. I'll add it

---

📁 include/boost/geometry/strategies/cartesian/densify.hpp

```diff
  47 |+             >::type calc_t;
  48 |+ 
  49 |+         typedef model::point<calc_t, 2, cs::cartesian> point2d_t;
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:02:22 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160684559  

Is this necessary? Can we not use Point for this?   
**EDIT:** It will then maybe also work for 3D points.

> Username: awulkiew  
> Created_at: 2018-01-10 18:39:08 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160763270  

It is necessary in order to use `calc_t` in calculations. The rationale behind dimension 2 is explained in the comment below:  
  
    // TODO: For consistency with spherical and geographic 2d length is  
    // taken into account. This probably should be changed. Also in the  
    // other strategies dimensions > 2 should be taken into account.  
  
I have no simple answer regarding this. But in order to be consistent with other strategies we should know how to calculate length in e.g. geographic with height.

> Username: awulkiew  
> Created_at: 2018-01-10 22:35:25 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160818060  

I implemented N-dimensional version which means that in cartesian strategy the length threshold is WRT N-dimensional segment. So it's different than in spherical and geographic.

---

📁 include/boost/geometry/strategies/cartesian/densify.hpp

```diff
  66 |+         // NOTE: Normalization will not work for integral coordinates
  67 |+         // normalize
  68 |+         //geometry::divide_value(dir01, len2d);
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:12:22 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160687085  

Indeed. We can create a compile time error for integer coordinates, or is that already done?

> Username: awulkiew  
> Created_at: 2018-01-10 19:02:00 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160769106  

No, there is no need for that. Below equivalent formula is used. It's requires more operations since vector multiplication and division is done each iteration. An alternative would be to use it only for integral coordinates and for FP or user-defined coordinates used normalized vector. But I'm not sure if it's that important. What do you think?

> Username: barendgehrels  
> Created_at: 2018-01-10 20:58:31 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160796461  

No, I thought integer was not supported at all. Now I understand, thanks. So you can ignore my comment.

---

📁 include/boost/geometry/strategies/cartesian/densify.hpp

```diff
  37 |+ {
  38 |+     template <typename Point, typename AssignPolicy, typename T>
  39 |+     static inline void apply(Point const& p0, Point const& p1, AssignPolicy & policy, T const& length_threshold)
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:19:08 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160688872  

This interface looks OK, with still the comment that AssignPolicy can maybe combined with our mutable_range concept (which is adaptable)

> Username: awulkiew  
> Created_at: 2018-01-10 18:33:34 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160761863  

Are you suggesting that instead of AssignPolicy a MutableRange could simply be passed into the strategy? If that's the case then this was the original design but I changed it to get rid of Range-related code from strategies and have it only in the algorithm.

> Username: barendgehrels  
> Created_at: 2018-01-10 18:49:26 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160765885  

It is indeed a suggestion, but I did not verify if it is really possible or more convenient. So a "maybe", just to verify if what we have is OK or if this is better.


📁 include/boost/geometry/algorithms/densify.hpp

```diff
  52 |+ private:
  53 |+     Range & m_rng;
  54 |+ };
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:06:19 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160685493  

I think this duplicates  <boost/geometry/core/mutable_range.hpp>  
Can you verify if that can be used instead?

> Username: awulkiew  
> Created_at: 2018-01-10 18:20:10 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160758487  

`mutable_range.hpp` implements default MutableRange traits for pushing-back, resizing and clearing of range. Traits which users can specialize if needed. That's all. For convenience we then have functions like `bg::range::push_back()` using these traits.  
  
On the other hand AssignPolicy works like a function object or callback function allowing to define how the intermediate points created by the strategy are handled. It's here to have mutable Range operations only in the algorithm, not in strategies. This way the strategies don't depend on any concept more complex than a Point.

---

📁 include/boost/geometry/algorithms/densify.hpp

```diff
  85 |+             convert_and_push_back(rng_out, p0);
  86 |+ 
  87 |+             strategy.apply(p0, p1, policy, len);
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:07:55 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160685919  

If combination of segment length and densify_length results in a fraction, is than p1 missing?  
  
**EDIT:** Ah, AppendLastPoint might take care of that.

> Username: awulkiew  
> Created_at: 2018-01-10 18:27:53 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160760437  

Any calculation related to lengths is done in strategies. The strategy only generates intermediate points which means that it may generate no points and that the responsibility to return segment's endpoints is not the algorithm's side.  
  
AppendLastPoint is here to distinguish between open and closed geometries. And yes, if the strategy doesn't generate any points then the condition below appends the last endpoint.

---

📁 include/boost/geometry/algorithms/densify.hpp

```diff
  69 |+                              T const& len, Strategy const& strategy)
  70 |+     {
  71 |+         typedef typename boost::range_value<Geometry>::type point_t;
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:10:02 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160686448  

Alternatively you can state here:  
if (boost::empty(rng))  
{  
  return;  
}  
  
Anyway, otherwise please make these count variables const, and use curly braces

> Username: awulkiew  
> Created_at: 2018-01-10 18:20:28 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160758559  

I'll use `boost::empty()` and rewrite the algorithm to use range iterator instead of an index `i` and `range::at()` calls.

---

📁 include/boost/geometry/algorithms/densify.hpp

```diff
 362 |+ {
 363 |+     densify(geometry, out, max_distance, default_strategy());
 364 |+ }
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:16:58 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160688317  

Calling densify looks good, as expected. Thanks.  
The Geometry type input/output is the same, that is most probably OK, in simplify it is the same, for example. But the code in strategy assumes that it can be different too. Anyway, that is not a problem.

> Username: awulkiew  
> Created_at: 2018-01-10 18:31:32 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160761379  

Yes, originally I intended to allow passing different types. E.g. if the input is Segment the output has to be Linestring or MultiLinestring. But in order to make it simpler for now I decided to mimic `simplify`'s interface.


📁 test/algorithms/densify.cpp

```diff
 133 |+     bg::densify(g, o, max_distance, d.compl_s);
 134 |+ 
 135 |+     // geometry was indeed complexified
```

> Username: barendgehrels  
> Created_at: 2018-01-10 14:13:28 UTC  
> Updated_at: 2018-01-15 18:47:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#discussion_r160687387  

densified


---

## Comment 9

> Username: barendgehrels  
> Created_at: 2018-01-10 18:50:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-356699422  

@awulkiew  thanks for your answers!

---

## Comment 10

> Username: vissarion  
> Created_at: 2018-01-11 15:10:34 UTC  
> Updated_at: 2018-01-11 15:11:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-356962641  

Thanks Adam! Some comments:  
  
Should you add the header of strategies and algorithms to strategies.hpp and geometry.hpp respectively?  
  
Testing the algorithm a bit, for LINESTRING(0 0,1 1) and max distance=1 the lengths are as following for geographic:  
  
156897.7995 (original)  
157096.5918 (vincenty)  
157095.4633 (thomas)  
157094.8629 (andoyer)  
  
while for spherical with max distance=0.01:  
  
157249.5978 (original)  
157249.5978 (densify)  
  
This is expected since the algorithm for spherical uses a more direct computation and not this navigation-like construction of new points that highly depends on the computed azimuth. Interestingly, the different strategies for direct and inverse computation does not affect a lot the result (compared to the length error w.r.t. the original segment). Moreover, we observe that less accurate methods (i.e. andoyer) has smaller error than more accurate ones (i.e. vincenty).

---

## Comment 11

> Username: awulkiew  
> Created_at: 2018-01-11 18:32:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-357019230  

> Should you add the header of strategies and algorithms to strategies.hpp and geometry.hpp respectively?  
  
Yes, it should. I just added it.  
  
> Testing the algorithm a bit (...)  
  
Yes, spherical strategy works with 3d cartesian vectors and geographic strategy use inverse and direct geodesic formulas to calculate intermediate points. So geographic strategy strongly depends on the accuracy of the azimuth calculation.  
  
The segment from your example is 150km long and max_distance is 1m, so densified geometry will have around 150k points. I'm not sure which densify strategies were used in your example, default ones or corresponding to distance strategies and which distance strategy was used to calculate the length of the original. From the values I've got below (though with presumably different compiler) you probably densified geometry using 3 different formulas but in all cases calculated distance with default strategy using andoyer formula.  
  
Out of curiosity I checked all combinations of strategies. Again for LINESTRING(0 0,1 1) and max_distance 1m would be (with msvc-14):  
  
| length\geometry | original | densified, andoyer | densified, thomas | densified, vincenty |  
| ----- | ----- | ----- | ----- | ----- |  
| andoyer | 156897.7995 | **157094.8449** | 157095.4722 | 157096.6099 |  
| thomas | 156899.5679 | 156845.6776 | **156845.7738** | 156845.7738 |  
| vincenty | 156899.5683 | 156899.7018 | 156899.5653 | **156899.5653** |  
  
and for a longer segment LINESTRING(0 0,40 40) and max_distance 40m to keep the number of points similar:  
  
| length\geometry | original | densified, andoyer | densified, thomas | densified, vincenty |  
| ----- | ----- | ----- | ----- | ----- |  
| andoyer | 6002863.0868 | **6002869.8517** | 6002862.6190 | 6002862.4907 |  
| thomas | 6002893.9521 | 6002901.2457 | **6002893.9611** | 6002893.9623 |  
| vincenty | 6002893.9612 | 6002901.1906 | 6002893.8999 | **6002893.8998** |  
  
So the results looks as expected i.e. less accurate formulas give less accurate results. Note that the loss in accuracy occurs both at the densify and at the distance calculation stages.  
  
> Moreover, we observe that less accurate methods (i.e. andoyer) has smaller error than more accurate ones (i.e. vincenty).  
  
In this case, and if you indeed used the strategies I think you used (andoyer for distance in every case), the least precise part is the length calculation of a linestring containing ~150k points because the inaccuracies are accummulated in the result and the accumulated error of the result in all three cases may be greater than the error of andoyer formula for the original segment. So indeed in this case the observation is correct however I think the reason is as described above and I doubt that this is the general rule (that theoretically least precise formula is the most precise in practice).

---

## Comment 12

> Username: vissarion  
> Created_at: 2018-01-12 10:40:17 UTC  
> Updated_at: 2018-01-12 10:41:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-357204489  

Indeed as you said, I used the default strategy for length (i.e. andoyer) and varied the densify strategies. Thanks for the clarification. My main question is still: why using andoyer formula for length the result on vincenty densified segment is less accurate than the one on andoyer densified segment. But I see that is of little interest and it only happens in the first table above, thus I am OK with it.  
  
Finally, it seems that even with very densely densified segments (as the ones above) the results have some good accuracy (when vincenty is used). Do you think the more accurate/iterative method you described above worth to be implemented? It should give even more accurate results.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2018-01-12 14:59:37 UTC  
> Updated_at: 2018-01-12 17:29:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-357260363  

Regarding the other algorithm. Currently the algorithm [1] is iterative, i.e. first the distance and azimuth is calculated (1* inverse) and then N points are generated on the segment (N* direct). Alternative algorithm [2] would be to divide segment recursively so for each division calculate inverse and direct (N* inverse and N* direct). So my guess is that algorithm 2 would be more accurate.  
  
Now, the accuracy of the result is heavily influenced by the accuracy of the azimuth calculation. Theoretically the least accurate point generated by algorithm [1] would be the point furthest from the first point (passed into direct solution). So I've done an experiment comparing both. In this experiment the last intermediate point is calcualted as if it was generated by the recursive algorithm [2]. So by generating a midpoint and then performing the same for the second part of the divided segment and doing that until the length of the segment is smaller than the threshold (below it's called recursive). And for comparison an algorithm which calculates the length of the original segment once, then only calculates the distance recursively to get the distance to the intermediate point as if the segment was divided into half recursively and then performing direct calculation once using this distance (it's called direct) as if it was used by algorithm [1]. In all cases I was using the formulas consistently in algorithms length, distance and densify. The results are:  
  
LINESTRING(0 0, 1 1), max_distance = 1000  
  
|  | andoyer | thomas | vincenty |  
| ----- | ----- | ----- | ----- |  
| P recursive | POINT(0.9960924403 0.9960939793) | POINT(0.9960929659 0.9960941554) | POINT(0.9960929665 0.9960941549) |  
| P direct | POINT(0.996076184 0.9960885498) | POINT(0.9960929443 0.9960941702) | POINT(0.9960929666 0.9960941549) |  
| distance Pr, Pd | 1.909033441 | 0 | 1.917440022e-06 |  
| azimuth Original | 0.7886744754 | 0.7886800657 | 0.7886800845 |  
| azimuth Pr | 0.7886742994 | 0.7886800651 | 0.7886800845 |  
| azimuth Pd | 0.7886688661 | 0.7886800469 | 0.7886800845 |  
| azimuth diff Pr, O | 1.759724411e-07 | 5.907350165e-10 | 1.505906511e-12 |  
| azimuth diff Pd, O | 5.609293959e-06 | 1.883517864e-08 | 2.123856646e-13 |  
  
LINESTRING(0 0, 1 1), max_distance = 1  
  
|  | andoyer | thomas | vincenty |  
| ----- | ----- | ----- | ----- |  
| P recursive | POINT(0.9999961845 0.9999961865) | POINT(0.9999961862 0.9999961873) | POINT(0.9999961845 0.9999961857) |  
| P direct | POINT(0.9999793362 0.9999905586) | POINT(0.9999961622 0.999996201) | POINT(0.9999961845 0.9999961857) |  
| distance Pr, Pd | 1.976360196 | 0 | 1.653862412e-06 |  
| azimuth Original | 0.7886744754 | 0.7886800657 | 0.7886800845 |  
| azimuth Pr | 0.788674475 | 0.7886800657 | 0.7886800845 |  
| azimuth Pd | 0.7886688661 | 0.7886800469 | 0.7886800845 |  
| azimuth diff Pr, O | 3.850268993e-10 | 1.292077556e-12 | 5.941913628e-13 |  
| azimuth diff Pd, O | 5.609289485e-06 | 1.883510437e-08 | 2.124966869e-13 |  
  
LINESTRING(0 0, 40 40), max_distance = 40  
  
|  | andoyer | thomas | vincenty |  
| ----- | ----- | ----- | ----- |  
| P recursive | POINT(39.99978665 39.99987507) | POINT(39.99978666 39.99987505) | POINT(39.99978666 39.99987505) |  
| P direct | POINT(39.99926523 39.9999428) | POINT(39.99978643 39.99987526) | POINT(39.99978666 39.99987505) |  
| distance Pr, Pd | 45.15685564 | 0 | 5.228705701e-06 |  
| azimuth Original | 0.6564162542 | 0.6564226471 | 0.6564226524 |  
| azimuth Pr | 0.6564162538 | 0.6564226471 | 0.6564226524 |  
| azimuth Pd | 0.6564098552 | 0.6564226411 | 0.6564226524 |  
| azimuth diff Pr, O | 4.237984408e-10 | 1.985078768e-13 | 8.326672685e-14 |  
| azimuth diff Pd, O | 6.399017782e-06 | 5.999675867e-09 | 5.773159728e-14 |  
  
So looking at azimuths the recursive algorithm seems to be more accurate for andoyer and thomas but less accurate for vincenty. However the difference between algorithms is similar to the accuracy of formulas. E.g. the difference between points for shorter linestring and andoyer is around 2m so similar as in length of the whole segment for andoyer v.s. vincenty (156897.7995 v.s. 156899.5683). EDIT: Similar for original azimuths (0.7886744754 v.s. 0.7886800845, so difference 5.6e-6).  
  
So it looks like the implementation of recursive algorithm is not needed. However it looks like the geometry densified with recursive algorithm is more accurate representation, at least for less precise algorithms. So I guess it could be used as a simple way of "improving" the geometry before passing it into some other algorithm. What do you think?  
  
Btw, it probably has no sense to pass a threshold smaller than the error of the formula. E.g. 1m threshold for formula with 20m error. So it's probable that for andoyer the only reasonable results are the ones for max_distance = 1000.

---

## Comment 14

> Username: vissarion  
> Created_at: 2018-01-12 15:50:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-357274678  

It seems that it is not needed. Thanks for the details.

---

## Comment 15

> Username: awulkiew  
> Created_at: 2018-01-12 17:31:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/437#issuecomment-357303058  

Btw, distance calculation using thomas formula seems to be wrong in all 3 cases.

---
