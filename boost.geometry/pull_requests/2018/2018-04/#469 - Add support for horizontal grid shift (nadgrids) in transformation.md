# #469 Add support for horizontal grid shift (nadgrids) in transformation. [Merged]

> Username: awulkiew  
> Created at: 2018-04-07 13:59:36 UTC  
> Updated at: 2018-05-10 16:02:41 UTC  
> Merged at: 2018-05-10 16:02:41 UTC  
> Closed at: 2018-05-10 16:02:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/469  

This PR adds support for nadgrids. Relevant proj4 code was converted to C++ and Boostified. So all grids formats are supported (ctable, ctable2, ntv1, ntv2), also vertical geoid grid GTX format but it is not used right now. There are some differences though:  
- proj4 stores loaded grids in global storage and pointers to a relevant subset of grids in projection parameters structure. In Boost.Geometry grids are moved outside transformation to allow users to place grids storage(s) wherever they like.  
- in proj4 the grids are loaded implicitly when there is `+nadgrids` parameter passed. In Boost.Geometry an object representing a subset of grids explicitly has to be initialized and then passed into transforming function.  
- in proj4 grids has to be "installed" into certain directories. In Boost.Geometry the user can implement StreamPolicy opening any input stream having unformatted input interface. The default one uses `std::ifstream` opening files having the same names as the ones in `+nadgrids` parameter in working directory.  
  
I'm open to suggestion, in particular regarding the interface, names, etc.  
  
This is how it looks like right now by default:  
  
    std::string from = "+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs";  
    std::string to = "+proj=aea +lat_1=34 +lat_2=40.5 +lat_0=0 +lon_0=-120 +x_0=0 +y_0=-4000000 +ellps=clrk66 +datum=NAD27 +units=m +no_defs";  
    // +datum=NAD27 uses +nadgrids=@conus,@alaska,@ntv2_0.gsb,@ntv1_can.dat  
  
    bg::srs::transformation<> tr = {bg::srs::proj4(from), bg::srs::proj4(to)};  
  
    bg::srs::grids_storage<> gs;  
  
    // reads headers of grids, adds them to grids_storage  
    // and creates representation of grids that are used by this transformation  
    bg::srs::transformation_grids<> tg = tr.initialize_grids(gs);  
  
    point_geo san_fran(-122.419167, 37.779167);  
    point_xy xy;  
  
    tr.forward(san_fran, xy);  
    std::cout << "without grids: " << bg::wkt(xy) << std::endl;  
  
    // loads content of relevant grid if needed and stores it in grids_storage  
    tr.forward(san_fran, xy, tg);  
    std::cout << "with grids: " << bg::wkt(xy) << std::endl;  
  
Now let's say a user wants to pass his own StreamPolicy opening files from directory "/abc" and share single grid storage between multiple threads:  
  
    struct my_stream_policy  
    {  
        typedef std::ifstream stream_type;  
  
        static inline void open(stream_type & is, std::string const& gridname)  
        {  
            is.open(std::string("/abc/") + gridname, std::ios::binary);  
        }  
    };  
  
    typedef bg::srs::grids_storage  
        <  
            my_stream_policy,  
            bg::srs::shared_grids  
        > my_grids_storage;  
  
    typedef bg::srs::transformation_grids  
        <  
            my_grids_storage  
        > my_transformation_grids;  
  
    // the rest is similar  
    my_grids_storage gs;  
  
    boost::thread t1{[&](){  
        bg::srs::transformation<> tr = {bg::srs::proj4(from1), bg::srs::proj4(to1)};  
        my_transformation_grids tg = tr.initialize_grids(gs);      
        for (int i = 0 ; i < 1000 ; ++i)  
            tr.forward(san_fran, xy, tg);  
    }};  
    boost::thread t2{[&](){  
        bg::srs::transformation<> tr = {bg::srs::proj4(from2), bg::srs::proj4(to2)};  
        my_transformation_grids tg = tr.initialize_grids(gs);  
        for (int i = 0 ; i < 1000 ; ++i)  
            tr.forward(san_fran, xy, tg);  
    }};  
    t1.join();  
    t2.join();  
  
Note that sharing the same transformation object between threads should work too because neither `transformation` nor `transformation_grids` do not change while the transformation is performed:  
  
    my_grids_storage gs;  
    bg::srs::transformation<> tr = {bg::srs::proj4(from), bg::srs::proj4(to)};  
    my_transformation_grids tg = tr.initialize_grids(gs);  
    boost::thread t1{[&](){  
        for (int i = 0 ; i < 1000 ; ++i)  
            tr.forward(san_fran, xy, tg);  
    }};  
    boost::thread t2{[&](){  
        for (int i = 0 ; i < 1000 ; ++i)  
            tr.forward(san_fran, xy, tg);  
    }};  
    t1.join();  
    t2.join();  
  
This PR is implemented on top of https://github.com/boostorg/geometry/pull/468 and while the other PR is not merged this one will contain some unrelated changes.

---

## Comment 1

> Username: vissarion  
> Created_at: 2018-04-20 08:15:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/469#issuecomment-383020343  

It seems ok to me. Thanks!  
One general comment, how Boost.Geometry is going to keep track of updates on proj4 (or proj5 or any later version)?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-04-20 16:44:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/469#issuecomment-383154882  

Currently all updates have to be made manually.  
  
When projections were first converted to Boost.Geometry (AFAIR it was proj4 4.9.1) at least the code containing projections was converted automatically. However the code in proj4 was changed since then so I'd expect the old converter does not work anymore. Besides, I don't know where it is and how easy would it be to update it. Plus the rest of the code has to be updated manually anyway as it was in the past.

---
