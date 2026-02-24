# #961 Add Web Mercator projection [Merged]

> Username: vissarion  
> Created at: 2022-01-13 10:33:44 UTC  
> Updated at: 2022-02-14 15:51:58 UTC  
> Merged at: 2022-02-14 15:51:53 UTC  
> Closed at: 2022-02-14 15:51:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/961  

This PR adds the Web Mercator / Pseudo Mercator projection a variant of the regular Mercator projection. For computations the Web Mercator projection uses the formulas of Mercator projection.  
  
More info:  
- https://proj.org/operations/projections/merc.html  
- https://proj.org/operations/projections/webmerc.html

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2022-01-13 11:48:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/961#pullrequestreview-851607901  

📁 include/boost/geometry/srs/projections/proj/webmerc.hpp

```diff
  21 |+ #include <boost/geometry/srs/projections/impl/projects.hpp>
  22 |+ 
  23 |+ #include <boost/geometry/srs/projections/proj/merc.hpp>
```

> Username: awulkiew  
> Created_at: 2022-01-13 11:48:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#discussion_r783885168  

Note that if this file is included then the includes above and below are not needed.  
  
But I'd propose to define this projection in the same file as merc projection. This is how it's done with projections sharing the same bases and this is how it's done in proj.

> Username: awulkiew  
> Created_at: 2022-01-13 11:57:48 UTC  
> Updated_at: 2022-01-13 11:57:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#discussion_r783891361  

See e.g. [sts.hpp](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/proj/sts.hpp#L245-L263) where 4 projections are defined.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2022-01-13 11:54:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1012067526  

Thanks!  
Are there any EPSG codes using this projection?  
If there are then you could add them here:  
- [dynamic](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/epsg.hpp)  
- [static](https://raw.githubusercontent.com/boostorg/geometry/develop/include/boost/geometry/srs/projections/epsg_traits.hpp)  
  
I only don't know what to do with the old ones where `merc` is used while `webmrec` probably should. Should they be modified/replaced?  
@mloskot what do you think?

---

## Comment 3

> Username: vissarion  
> Created_at: 2022-01-13 15:21:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1012236513  

> Thanks! Are there any EPSG codes using this projection? If there are then you could add them here:  
>   
>     * [dynamic](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/srs/projections/epsg.hpp)  
>   
>     * [static](https://raw.githubusercontent.com/boostorg/geometry/develop/include/boost/geometry/srs/projections/epsg_traits.hpp)  
  
I only know EPSG:3857 which is Web Mercator with WGS84 as a base ellipsoid.   
https://epsg.org/crs_3857/WGS-84-Pseudo-Mercator.html  
I added that.   
   
>   
> I only don't know what to do with the old ones where `merc` is used while `webmrec` probably should. Should they be modified/replaced? @mloskot what do you think?  
  
Do you have an example of an old one?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2022-01-13 22:49:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1012587200  

> Do you have an example of an old one?  
  
Deprecated EPSG 3785, see: https://github.com/boostorg/geometry/issues/657

---

## Comment 5

> Username: vissarion  
> Created_at: 2022-01-14 13:00:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1013097220  

> EPSG 3785  
  
I changed it to be the same as in EPSG: 3857 see https://github.com/boostorg/geometry/pull/961/commits/6d22df0850668504878af1b62b824230264bbc63

---

## Comment 6

> Username: awulkiew  
> Created_at: 2022-01-14 15:39:34 UTC  
> Updated_at: 2022-01-14 15:39:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1013233074  

Ok thanks. Defining it as the same projection is one of the possible solutions. I don't know if this is the correct approach but I'm ok with it.  
  
Minor detail: the last commit message is using a different format. It should've been `[srs] Set ...` or `[projections] Set ...`, not `Setting ...`.

---

## Comment 7

> Username: mloskot  
> Created_at: 2022-01-17 10:21:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1014360378  

Sorry guys, I was taking a week off.  
  
Yes, the `EPSG:3857` is the official code, but there is a collection of historical 'aliases', here is complete list https://wiki.openstreetmap.org/wiki/EPSG:3857

---

## Comment 8

> Username: vissarion  
> Created_at: 2022-01-17 10:40:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1014376904  

> Sorry guys, I was taking a week off.  
>   
> Yes, the `EPSG:3857` is the official code, but there is a collection of historical 'aliases', here is complete list https://wiki.openstreetmap.org/wiki/EPSG:3857  
  
thanks @mloskot !  
  
I also replaced the deprecated 3587 but the rest of the list (esri and osgeo) are not supported by boost geometry. Does it make sense to support them and bind them to the same behavior are webmerc, or we simply keep avoiding them. I am in favor of the latter.

---

## Comment 9

> Username: mloskot  
> Created_at: 2022-01-17 10:59:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1014391788  

@vissarion I'd also suggest to avoid them. The sooner users stop using them, globally, the better :)

---

## Review 10 [Approved]

> Username: barendgehrels  
> Created_at: 2022-01-19 09:36:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/961#pullrequestreview-856525871  

Cool!  
Is it 100% compatible with Web Mercator google uses?  
Can we just combine it and overlay our maps over it?

---

## Comment 11

> Username: vissarion  
> Created_at: 2022-01-24 16:10:33 UTC  
> Updated_at: 2022-01-24 16:11:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1020266204  

> Cool! Is it 100% compatible with Web Mercator google uses?   
  
It is not clear what Google uses in its several map services and apps. According to [wikipedia](https://en.wikipedia.org/wiki/Google_Maps) in `Google maps` a variant of the mercator projection was used in the past while the `Google maps platform` [uses](https://developers.google.com/maps/documentation/javascript/examples/map-coordinates) the web mercator projection similar to the one implemented in Boost Geometry up to some constants and rounding.   
  
> Can we just combine it and overlay our maps over it?  
  
That sounds cool, do you have a specific project in mind?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2022-02-02 10:49:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/961#issuecomment-1027809485  

> > Cool! Is it 100% compatible with Web Mercator google uses?  
>   
> It is not clear what Google uses in its several map services and apps. According to [wikipedia](https://en.wikipedia.org/wiki/Google_Maps) in `Google maps` a variant of the mercator projection was used in the past while the `Google maps platform` [uses](https://developers.google.com/maps/documentation/javascript/examples/map-coordinates) the web mercator projection similar to the one implemented in Boost Geometry up to some constants and rounding.  
>   
> > Can we just combine it and overlay our maps over it?  
>   
> That sounds cool, do you have a specific project in mind?  
  
Yes, I think it would be cool! But I didn't plan something until now... Maybe we can make an example, like we've more of them.

---
