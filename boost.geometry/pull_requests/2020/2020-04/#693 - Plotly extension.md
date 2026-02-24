# #693 [WIP] [Feature] Plotly extension [Open]

> Username: sudo-panda  
> Created at: 2020-04-05 17:55:20 UTC  
> Updated at: 2025-05-15 19:51:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/693  

I have currently added support for 3D points. Support for others will be added soon.  
  
This extension just outputs json. If you want to view it you will require to [wrap it in html](https://gist.github.com/sudo-panda/8de1669777fe0e1670b06d5a9e94152b).

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-04-20 13:11:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-616543164  

Do I understand correctly that Plotly is only able to draw plots, so it is not for drawing geometries and what this PR does is allowing to represent Boost.Geometry linear geometry as a scattered line plot?  
  
If that's the case I don't understand how this is useful. A linestring, a geometry, is not a plot line. Plus AFAIU it is not possible to draw other geometries. What is the purpose of using Plotly with Boost.Geometry?

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-04-20 13:17:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-616546824  

I started brainstorming use of plotly (or others) for general shapes drawing, see https://gitter.im/boostorg/geometry?at=5e6d0b8e17d3e742347c5346  
  
Since plotly can draw geometric shapes, https://plotly.com/python/shapes/, as well as can draw on top of popular maps, https://plotly.com/python/maps/, evaluating its API for general purpose visualisation of geometries seems to me like a valid idea.

---

## Comment 3

> Username: sudo-panda  
> Created_at: 2020-04-20 13:21:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-616548919  

> Do I understand correctly that Plotly is only able to draw plots, so it is not for drawing geometries and what this PR does is allowing to represent Boost.Geometry linear geometry as a scattered line plot?  
>   
> If that's the case I don't understand how this is useful. A linestring, a geometry, is not a plot line. Plus AFAIU it is not possible to draw other geometries. What is the purpose of using Plotly with Boost.Geometry?  
  
The main purpose of using plotly is for its mesh plot and it's UI. We can represent every geometry as a plot except that polygons won't be filled in the 2D plot. But plotly is great for showing 3D plots and mesh plots too. And if someone really wants the polygon to be filled, (which can only be done in 2D) they can use the existing svg_mapper. This plugin adds the functionality of displaying 3D geometries which is unavailable in svg mapper.

---

## Comment 4

> Username: sudo-panda  
> Created_at: 2020-04-20 14:36:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-616595033  

> I started brainstorming use of plotly (or others) for general shapes drawing, see https://gitter.im/boostorg/geometry?at=5e6d0b8e17d3e742347c5346  
>   
> Since plotly can draw geometric shapes, https://plotly.com/python/shapes/, as well as can draw on top of popular maps, https://plotly.com/python/maps/, evaluating its API for general purpose visualisation of geometries seems to me like a valid idea.  
  
Thanks I needed this. The chart-studio of plotly creates area charts with the the x and y-axis as borders. So I thought it isn't possible to fill polygons. Maybe there is some other way. I will look into that. Thanks a lot.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-04-20 14:43:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-616599349  

@mloskot Right, I'm not familiar with Plotly, thanks. So it is possible to represent geometries and (I saw gitter discussion) @sudo-panda needs to display 3D geometries.  
  
> as well as can draw on top of popular maps  
  
Do I understand correctly that Plotly use their own data format based on Json? Or did they adapt some well known format for representing geometrical data?  
  
Why not use widely known solution for that (at least according to my limited knowledge of the subject), i.e. GeoJSON? Aside from displaying on maps it would allow to import geometries in various databases and GIS programs?  
  
I have nothing against adding Plotly IO. I'm just confused why this particular format was chosen. I also don't know how widely used it is. If it is well known and popular it could be added it in the official release instead of extensions.

---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-20 14:46:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/693#pullrequestreview-396524124  

📁 include/boost/geometry/extensions/io/plotly/plotter.hpp

```diff
 215 |+     }
 216 |+ 
 217 |+     virtual ~json_plotter()
```

> Username: awulkiew  
> Created_at: 2020-04-20 14:46:22 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r411438205  

Virtual destructor is not needed here, is it?


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-20 14:54:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/693#pullrequestreview-396531491  

📁 include/boost/geometry/extensions/io/plotly/plotter.hpp

```diff
 211 |+         m_stream << "{"
 212 |+                 << std::endl
 213 |+                 << "    data: [";
```

> Username: awulkiew  
> Created_at: 2020-04-20 14:54:11 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r411444576  

Newlines and indentations are only for human readability. I propose to make them optional. I also propose to replace `std::endl` with `'\n'` because you don't need to flush the stream each time you go to the newline.


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-20 15:14:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/693#pullrequestreview-396550104  

📁 include/boost/geometry/extensions/io/plotly/plotter.hpp

```diff
 201 |+ class json_plotter : boost::noncopyable
 202 |+ {
 203 |+     std::ostream& m_stream;
```

> Username: awulkiew  
> Created_at: 2020-04-20 15:14:00 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r411460436  

Ok, so the stream has to live outside this class. It is like in `svg_mapper` so I understand that you replicated the existing design. This is error-prone since when the object outside is destroyed then this is a dangling reference.  
  
So I'm wondering about a different design, i.e. owning the stream instead of the reference. E.g.:  
```  
template <typename Stream>  
class plotly_plotter  
{  
public:  
    template <typename ...Ts>  
    plotly_stream(Ts &&... params)  
        : m_stream(std::forward<Ts>(params)...)  
    {}  
private:  
    Stream m_stream;  
};  
```  
  
What do you think? Do you have different ideas?

> Username: sudo-panda  
> Created_at: 2020-04-21 16:33:47 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r412310287  

Yeah that seems better. I was thinking why the above design was implemented in `svg_mapper`. The only reason I could find is if some external changes are required by the user that isn't supported by `svg_mapper`. This isn't required by `plotly_plotter` at all then.  Can you tell me that actual reason for this design?

> Username: awulkiew  
> Created_at: 2020-04-21 21:48:34 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r412515115  

> Can you tell me that actual reason for this design?  
  
I don't really know I have only a few ideas though. @barendgehrels do you remember why exactly does the `svg_mapper` store the reference to the stream instead of owning it?  
  
Back then perfect forwarding was impossible. The code in my previous comment requires C++11 but Boost.Geometry right now requires C++03 (this will change soon). And various streams can have various interfaces, e.g. no `open()` member function so there must be a way of passing parameters into its constructor.  
  
In some cases the stream objects are already created, e.g. `std::cout`, `std::cerr`, etc. AFAIU it is possible to account for that by creating a `std::ostream` from another stream's `streambuf`:  
```  
plotly_plotter<std::ostream> plotter(std::cout.rdbuf());  
```  
But this would be more confusing than simply calling:  
```  
plotly_plotter plotter(std::cout);  
```  
  
With non-owning relationship the same stream can be shared by several mappers/plotters. I don't know if that makes sense but it is possible nevertheless.  
  
The user can keep and e.g. close and reopen `ofstream` buffer instead of creating the `svg_mapper`/`plotly_plotter` object each time.  
  
> The only reason I could find is if some external changes  
  
Right, though the owned stream could be exposed with some getter function if needed.  
  
Btw, I'm not saying that you should start implementing what I have proposed above. I'm only brainstorming. E.g. now after listing the potential reasons above I'm not sure whether or not my proposal was good.  
  
On the other hand maybe passing a reference as a template parameter could work with the design I proposed. So the user would be able to do both things, store a stream object or a reference. Unless there are some issues I'm not taking into account.  
```  
plotly_plotter<std::ostream &> plotter(std::cout);  
```  
  
And last but not least I'm not sure if the design should be different than `svg_mapper` because the users may expect that the tools work the same and either expect that a reference is stored in `plotly_plotter` or that an object is stored in `svg_mapper`. We should probably be consistent one way or another.  
  
@mloskot do you have some thoughts about it?

> Username: sudo-panda  
> Created_at: 2020-04-24 17:51:56 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r414756866  

For now I have put it inside the class itself as I think it's better to make it safe than just copy the existing design as:  
  
1) It is an extension so the design need not be matched with integral parts of Boost Geometry.  
2) I see no possible requirement for keeping it outside other than the one mentioned above as we are going to shift to C++11 soon.

> Username: mloskot  
> Created_at: 2020-04-24 18:05:14 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r414764956  

Although I can't remember exactly, I think @awulkiew has figured it out right.  
It was to allow user to plug in a stream object of their choice, and with simple interface.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-20 15:15:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/693#pullrequestreview-396551390  

📁 include/boost/geometry/extensions/io/plotly/plotter.hpp

```diff
 199 |+ 
 200 |+ 
 201 |+ class json_plotter : boost::noncopyable
```

> Username: awulkiew  
> Created_at: 2020-04-20 15:15:20 UTC  
> Updated_at: 2020-05-11 18:32:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#discussion_r411461537  

This name is too general I think. There are many formats based on JSON.


---

## Comment 10

> Username: mloskot  
> Created_at: 2020-04-20 15:21:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-616623386  

@awulkiew   
> Why not use widely known solution for that (at least according to my limited knowledge of the subject), i.e. GeoJSON? Aside from displaying on maps it would allow to import geometries in various databases and GIS programs?  
  
I did not suggest plotly as replacement for GeoJSON which on its own is a preferred geo format indeed.  
  
I considered plotly more as an alternative to SVG, for nice rendering of geometries, with axis, with scaling possibility, loadable into plotly-aware viewers, etc. I don't know if plotly does support GeoJSON well.  
  
By the way, there is also MapML, e.g. GDAL/OGR has just received support for it https://github.com/OSGeo/gdal/pull/2374  
  
I just thought exploring new ways to visualise geometries may be of interest to those looking for ways to start digging Boost.Geometry, who are keen in visualisations, I/O formats, etc.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2025-05-15 19:51:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/693#issuecomment-2884903748  

@sudo-panda : Can this be closed? There is no activity anymore (WIP for 5 years)

---
