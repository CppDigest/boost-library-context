# #955 - boost::geometry::intersection not working on polygon wholly contained in box [Closed]

> Username: meastp  
> Created at: 2021-12-21 13:35:55 UTC  
> Updated at: 2021-12-22 06:52:03 UTC  
> Closed at: 2021-12-22 06:50:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/955  

Hi,  
  
I'm trying to use intersection between a polygon and a box, which is not working, surprisingly (it should clearly be an intersection). The polygon is wholly contained inside the box. The source code is below, but I also have [the example on godbolt](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGErqSuADJ4DJgAcj4ARpjEEgDMpAAOqAqETgwe3r7%2ByanpAiFhkSwxcVyJdpgOGUIETMQEWT5%2BXAFVNQJ1DQRFEdGxCbb1jc05bcM9fSVlCQCUtqhexMjsHObxocjeWADUJvFuTgoExJisB9gmGgCCG1s7mPuHCidnF/FXt/cM2157BzcWAAjl5MJdrndvmZNr9Hs83FFUKkCAB6YCYVBsU4ATwAdAgkkkIdDYX8AYckSj0ZjscQcTSsZhTnhMApUSlQgQAPqqfGE4mfSE/clPQFUk6MukMjFMllsjmeHHAAQEokku4wh7/MWU5GS2XSqXM4is9lI1RqwVfKGasnw8X6tH8d7IBBWjXC25c3YsJihCBzYUAdist12Ed2BBxSUwWCouwlBBAIENJpxKZYGEwtBTKVoyoEjpRKbTuMz2dzIHQZjzqC5vJxgPQSyi9Euz2wu3zhYYBzDN0jUZjccwCaTpdp6YrWCrFuLJ0ncvpM5zKZrdYbfObrfbn07ifn8QHkKHPZVDF2wDOjH7p8jUQtie84OPXsHD6dS%2BlKfe6G5ADuADWBAQPeQ4RuYZjKAA8kEACaADiMHhFAACsABsZhoZIZi7BheIYZhAAcACc4KWBoGGkJh2GkVw%2BGEZhwbBhRFhUTRGHBvEkjEYxRG4WYbEcWhwZmFIfEERhkikTCwnUaJZjEVwwb8ZIwbEZI8k0WJpGYWpGnaaJ8RkRoanEfEaFGcGkhofEZlSbxZisdYIk2cGaEMY5YnEdZnlcHh3lya5CkeepXmEepkhcH56loQZwYYX5pEaJJkXETh1lEaR8TmbJ1nBlwaEOZFpG8QVZgaKpjmkZpBXqYFTEaDFIU6Yl3FqaRhmtaJFlcZ1GXWfRGGkfxxVaT1wZlWho1ScVZFDfEXVjUVSWTfRXAlURZgTZRoXEWREXbVhQ0aKRGFjfZ5E9cRGipbNTHxIlRm3QF1VMZI0UvXdFljWh/3ff9W3/ZlN2bfpc29S9XAHblkNWTdO2SMDM1CWDt1pURNm%2BWDXGNURB0I3tNHKRo0VjcRhVDWYy1zcpRPsftpFlRTZhrcTvW2fjaHKS1HMaRUD3ZRZ1kyR1UkYRoaP87Zd38ZL7OMzpkhYUdktlX5Klw4RksufzaFKWrm2K25MkVPLUjS0rokG8GW2q1bptlZjWF6aL0n20pOMy81LvxCp1k8ebEuYdd/OWc18sYVIgfxBhF0S1hJuhTTM1RwHk1mDTLsYfEjuhSpLOJ%2BJ1lcLnkjyzbpepRXIeU9XyOV2J1lVbZle5y3khLZXfPWyNhXxSHZNGSNPMJzr5U9SNn2DxPxHJ5x9HEfjREBSPy8YS7OHxOvbRbzxu8yfLMk71PGl58f9dnxo/2VzfI9cVwQvScpD/FbP8dPQvmFYSZ8tByPf6Nl5apxHtFaOICPIj2wrhMam8w6MzmEGLOpArw3gYEGN83xwyfhLKmKc5YQB/kAiBMCOCIL7CzrBBCyFUIQCeoVZejF1JS20gwmGjVl4aV2krQq0VUqMS4XVVqfDNppRYfnUg7CmEEQkfJJBUFUFtjBJgk85DdgnHQOuTAoJXxuHPEWfcSwCBJC8AQO86iJz4OXBmEAXJYhKE6AwCAyjMCoOvJgRgqDjGmIIKo8C74hw%2BjwM8AAIrsDQFiPwRk0SmNAZiESAkoWYDxjBKEYXMBdVxIAoKJMOBoggWiQCuFoFEocFgYIwSEAAFW5AAMRggAJWwDcNwAAJCABjLxoAYCcTJ3ZvFmN8UGdRJhQzgQgrElAxi8luF2Hgaw1hZnJJAMk5ZViywrhAA0c4nS5jLKmSUspkYxmhPfBM1EqJdihOqAYM4uwmAFPeCwB5DB0CvN2EIAAaohX0TAiSxAmVMt4oRgBvHOC8hQAA3YAxzILjMsV%2BaxP4QDQuANyP0ALiALmTMi6cIAsyznXLWEAnJBCNh3F4Nsr4rj7kxbGYgEA0WoM2hoFld1/HYOibsS5uwbjoHeZss0GivBuijAgJgBAHm0FoOKtkTwhVsl2DQKVmBVBMAcAWXYAg5V/OJOo%2BlsQ8RMAFdAdBnLuWGuIMa01riLUXKuaE4gTAAK6sVQobVl4CAICeN835mLUFeDSEYB5GjYzIDwDQZAnyfkFJxO2A1/yGV4kxWazxDBUFQRoDKgAtKgJIGrCAZg0HiNC/Zs25mIMAKIEAiqJEqtIDQqi3ioCAuwKtNa62kAbaQJt/YW1tpzQBPA6BvUgDMIo3YaELVDitSm/5LiXyZqzhWvNBaI3RhACW%2BI5a6CVurRATyNEAh1ubacVt7aD1HqPae/t57B3DtHUgCdWcZ2RkuXO1NyBl1mHzYWzdJbJC7plSABgAhXwWAHZemt4kzC9t7We4gF6h0jrHWW19USHXXLZKcUVRBiDaoTJi3Yw6ZWJieKIGVcZdg5oeQK0FCJURoo1BQ%2BFZz1E8sdTh4geGSCEaeRCkje7yO7Eo/QdAqDtj5BDd6p42bwRcqBYUuJMzARJLRXiN4gY4W7DOAQZYl5IlYLuMGUJHAFi5g4GhXgfgOBaFIKgTgbhFmWA0UsFYYoYQ8FIMmOz5mFhARAMVfQnBJA2c0LwRzHBeAKC3T5iLCw4CwBgIgEAPizHkEoGgFgSQ6CDACHGQgJA9D8EECIMQ7ApAyEEIoFQ6g/M%2BZvJwbzAFnVJGa%2BZkLVnwsNaizBIZCTUAJlUPPHN0krzIGjW0PEeEIAeBy3lgjGwuBzF4L5rQSDSCBeC5ZsLpAWAgF4niFS88KhLUlmTYMpBbP2aizFuL63OtJdS9l3L9AyAUAgK9xbqY2hwezQQBxlAogRdIFEUIDQMzcF4NltgggYIMALKDrAfojDiGh6QfAZwahQrZKDtV1QzFrHs/Yyz3naB4CiM6%2BkHgsCg5ZAd6HCwqAGDBV81kAEYKxls950rwhKOVekHz2rahQe6ACAYIwKAXM2Ap1EWLsBmBsBAEmUguO4jcA0v5hzSRHACFi9F2wOZqh678BAVwYxWiBDedMAY5Q8hpFN5bvQKRHcZFt6UfLRv7Cm%2B6KMTwLQ9AdF9yMXooR%2Bie/tycHozv2ih497MFbixlirD0KcTxHWLOcGs9d0HUWRsYTGxXYAk3djTdm85yw1hUG4CK0trzqD5tvdiJQ/2q34t%2Bc2z6k1gxAxdb2wdzyeJl4VHnpLJaelEg3ci5we7bLHube22yyz8Qeu3dnx3jbWeOBmDXzPw3C%2BFjq7SM4SQQA)  
  
I used https://www.svgviewer.dev and Inkscape to visualize the resulting svg.  
  
```cpp  
#include <iostream>  
#include <sstream>  
#include <deque>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/box.hpp>  
  
#include <boost/foreach.hpp>  
  
  
int main()  
{  
    typedef boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double> > polygon;  
    typedef boost::geometry::model::box<boost::geometry::model::d2::point_xy<double> > bbox;  
  
    polygon green;  
    bbox blue;  
  
    boost::geometry::read_wkt(  
        "POLYGON((562542 6.65689e+06,562591 6.65677e+06,567348 6.65422e+06,572148 6.64923e+06,572817 6.64784e+06,572956 6.6478e+06,573890 6.64835e+06,574530 6.64827e+06,574751 6.64728e+06,575112 6.64723e+06,575471 6.64741e+06,575475 6.64776e+06,575908 6.64854e+06,576593 6.64892e+06,577150 6.64948e+06,577207 6.64984e+06,577472 6.6501e+06,577673 6.64978e+06,578367 6.64985e+06,579169 6.6504e+06,579459 6.65089e+06,579397 6.65156e+06,579110 6.6524e+06,578891 6.65262e+06,579096 6.65309e+06,580089 6.65376e+06,580127 6.65441e+06,580083 6.65555e+06,580550 6.65554e+06,581056 6.65578e+06,581883 6.6555e+06,582440 6.65592e+06,581808 6.65748e+06,581672 6.65885e+06,581041 6.65871e+06,579297 6.65815e+06,578994 6.65826e+06,578452 6.65811e+06,578139 6.65983e+06,574973 6.6602e+06,574500 6.6606e+06,574621 6.66094e+06,575173 6.6607e+06,575281 6.66106e+06,574913 6.66142e+06,575270 6.66212e+06,574948 6.66295e+06,574640 6.66288e+06,574018 6.66317e+06,573413 6.66569e+06,573501 6.66614e+06,573366 6.66626e+06,572959 6.66617e+06,572298 6.66632e+06,571794 6.66621e+06,571634 6.66575e+06,571084 6.66587e+06,571040 6.66572e+06,570745 6.66563e+06,570439 6.6651e+06,569715 6.66504e+06,569586 6.6648e+06,569445 6.66486e+06,569182 6.66512e+06,568268 6.66543e+06,568118 6.66534e+06,568149 6.66493e+06,567832 6.66487e+06,567055 6.66505e+06,566719 6.66481e+06,566505 6.66376e+06,566238 6.66341e+06,565574 6.66295e+06,564161 6.66275e+06,562542 6.65689e+06))", green);  
  
    boost::geometry::read_wkt(  
        "POLYGON((377182 6.4702e+06,377182 6.82784e+06,714108 6.82784e+06,714108 6.4702e+06,377182 6.4702e+06))", blue);  
  
    std::deque<polygon> output;  
    boost::geometry::intersection(blue, green, output);  
     
  
    int i = 0;  
    std::cout << "green && blue:" << std::endl;  
    BOOST_FOREACH(polygon const& p, output)  
    {  
        std::cout << i++ << ": " << boost::geometry::area(p) << std::endl;  
    }  
  
  
    // Declare a stream and an SVG mapper  
    std::stringstream svg;  
    {  
    boost::geometry::svg_mapper<boost::geometry::model::d2::point_xy<double>> mapper(svg, 100, 100);  
  
    // Add geometries such that all these geometries fit exactly on the map  
    mapper.add(green);  
    mapper.add(blue);  
  
    // Draw the geometries on the SVG map, using a specific SVG style  
    mapper.map(green, "fill-opacity:0.5;fill:rgb(153,204,0);stroke:rgb(153,204,0);stroke-width:2", 5);  
    mapper.map(blue, "fill-opacity:0.3;fill:rgb(51,51,153);stroke:rgb(51,51,153);stroke-width:2");  
    //mapper.map(c, "opacity:0.4;fill:none;stroke:rgb(212,0,0);stroke-width:5");  
  
    // Destructor of map will be called - adding </svg>  
        }  
     // Destructor of stream will be called, closing the file  
  
  
    std::cout << svg.str();  
    return 0;  
}  
```  
![svgviewer-png-output](https://user-images.githubusercontent.com/1156416/146939144-aac55522-d435-493c-8153-61bfb2d0e89e.png)

---

## Comment 1

> Username: meastp  
> Created at: 2021-12-22 06:50:08 UTC  
> Updated at: 2021-12-22 06:52:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/955#issuecomment-999329032  

I'm sorry, this was user error -> `boost::geometry::is_valid(green)` is `false` because the direction of the Ring is CCW (and the polygon type's default direction is CW). Modifying the definition of the second polygon template type parameter `bool ClockWise` to false solved the issue.
