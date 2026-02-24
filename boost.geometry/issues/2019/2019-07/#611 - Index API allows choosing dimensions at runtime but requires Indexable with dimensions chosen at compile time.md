# #611 - Index API allows choosing dimensions at runtime but requires Indexable with dimensions chosen at compile time [Closed]

> Username: ericlavigne  
> Created at: 2019-07-14 06:26:56 UTC  
> Updated at: 2019-07-19 01:25:53 UTC  
> Closed at: 2019-07-19 01:25:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/611  

The spatial index library appears designed to allow the point dimensions to be selected at runtime, as shown in the following documentation page.  
  
https://www.boost.org/doc/libs/1_65_0/libs/geometry/doc/html/geometry/spatial_indexes/creation_and_modification.html#geometry.spatial_indexes.creation_and_modification.balancing_algorithms_run_time_parameters  
  
I am trying to take advantage of that feature, in a situation where I don't know at compile time the number of dimensions in each point.  
  
The problem is that Indexable types, such as point and tuple, seem to require the point's dimensions as a compile time parameter. I've searched the boost code for examples of dynamic_rstar, and found that it is used with Indexable whose dimension is set at compile time such as point<float, 2, bg::cs::cartesian> in libs/geometry/index/example/benchmark3.cpp.  
  
Is there any Indexable implementation I missed that allows the point's dimensions to be chosen at runtime?

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-07-14 13:11:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/611#issuecomment-511201587  

No. The parameters are for rtree balancing algorithms (like thresholds for insertion and removal per node, etc.), not the data stored. The dimension is defined at compile time as you noticed and the reason for that is because it's how it's designed in the whole library.  
  
However if you have a specific problem we can think about a solution.  
  
Would you like to store points of various dimensions in one rtree?  
Is the number of dimensions arbitrary in your application or do you only want to store e.g. 2d and/or 3d data?  
  
The most obvious solution is to have several rtree types, one for each dimension. If you only need to handle one dimension at a time you could put rtrees in Variant. If you need all of them at the same time, put them in Tuple instead. At this point you could write a simple wrapper using the correct tree for you in runtime automatically.  
  
You probably shouldn't use the rtree for higher dimensions anyway because of the increasing overlap of nodes. The number of reasonable dimensions for your specific datasets will be limited somehow. How? It depends on your specific case. For higher dimensions you should probably use a different data structure. Or maybe go for some X-tree implementation instead.

---

## Comment 2

> Username: ericlavigne  
> Created at: 2019-07-14 14:20:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/611#issuecomment-511206835  

I am creating a robotic planning library that would be used, for example, to control a self-driving car.  
  
https://github.com/ericlavigne/figurer  
  
If the car is alone on the road, the state dimension could be around 7 due to storing position (2), orientation (1), speed (1), acceleration (2), and jerk (1). If there are three other cars nearby, then that dimension would increase from 7 to 28 because we need similar information about each of the cars to be included in the state dimension.  
  
As part of the planning process, many states are evaluated for policy (what to do next from this state) and value (how good of an outcome to expect from this state). Before evaluating a new state, I would like to search for previously evaluated nearby states for two reasons: (1) if another state is close enough I might be able to avoid the evaluation entirely and use the other state instead and (2) if several states are fairly close I might be able to interpolate for a good starting estimate.  
  
"Would you like to store points of various dimensions in one rtree? Is the number of dimensions arbitrary in your application or do you only want to store e.g. 2d and/or 3d data?"  
  
At the moment that planning starts, the number of dimensions is fixed and known. But it is not known at compile time. The number of dimensions would vary, typically between 5 and 50.  
  
"You probably shouldn't use the rtree for higher dimensions anyway because of the increasing overlap of nodes. For higher dimensions you should probably use a different data structure. Or maybe go for some X-tree implementation instead."  
  
This library's benchmark speeds for 2D seem much faster than I need, so maybe the lower speed at higher dimensions will be good enough. I'll do some testing on that. Another possibility is to choose 5-10 of the most important dimensions for indexing, such as position, orientation, and speed of the main car and its closest neighbor.  
  
Is there a specific library you would recommend for higher dimension spatial indexing?

---

## Comment 3

> Username: awulkiew  
> Created at: 2019-07-15 18:04:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/611#issuecomment-511508243  

Thanks for the explanation. I think don't know enough to give you any advice regarding the solution. Theoretically it would be possible to write a recursive metafunction going through all possible dimensions e.g. 2 - 50 in compile-time and then running some code for compile-time dimension if run-time dimension is the same, e.g. at the beginning of some processing:  
  
```  
template <int Dimension = 2, int LastDimension = 51>  
struct process  
{  
    static void apply(int dimension)  
    {  
        if (I == dimension)  
        {  
            typedef bg::model::point<double, Dimension, bg::cs::cartesian> point;  
            bgi::rtree<point, bgi::rstar<4>> rtree;  
            // ...  
        }  
        else  
        {  
            process<Dimension + 1>::apply(dimension);  
        }  
    }  
};  
  
template <int LastDimension>  
struct process<LastDimension, LastDimension>  
{  
    static void apply(int dimension) {}  
};  
```  
  
but you'd have to test it to see if it'd work well for you. This would mean that in your program there would be ~50 similar code blocks one for each dimension so the program could be big.  And if you wanted to store rtrees between calls you'd have to keep ~50 of them in a recursive wrapper similar to Boost.Tuple (Boost.Tuple only allows 10 or 20 types) or maybe Boost.Fusion vector would be fine since AFAIR it allows more types.  
  
> This library's benchmark speeds for 2D seem much faster than I need  
  
The overlap is only a problem if it exists. It is possible that for your specific data there will be no overlap. Or maybe in your case the overlap would be very big in which case it would simply be better to keep all of the states in a vector and always search linearly. Who knows. Speed is unrelated to position and orientation. And I guess for cars driving on the same road the orientation is nearly the same for all of them and speeds are close as well, at least for cars driving on the same lane. Anyway, my point is, you should experiment with this.  
  
> Is there a specific library you would recommend for higher dimension spatial indexing?  
  
No, sorry. @vissarion do you?

---

## Comment 4

> Username: vissarion  
> Created at: 2019-07-16 07:08:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/611#issuecomment-511694579  

@ericlavigne indeed R-trees are not the best candidates for high dimensions. You can have a look at https://www.cs.umd.edu/~mount/ANN for moderate dimensions (up-to 20) and https://github.com/facebookresearch/faiss for higher dimensions.

---

## Comment 5

> Username: ericlavigne  
> Created at: 2019-07-19 01:25:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/611#issuecomment-513053333  

Thanks for the suggestions. It sounds like I will need something like FAISS for multi-car situations.  
  
I've spent several hours over the last few days build FAISS as a dependency of my project and am still having some difficulty with linking. Since I'm still at a prototype stage, I've decided to switch to a less performant homegrown solution just so I can continue with the rest of the project. In the likely event that I need better performance later, I'll make another attempt at using FAISS.
