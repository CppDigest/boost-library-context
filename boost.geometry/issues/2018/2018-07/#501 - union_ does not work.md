# #501 - union_ does not work. [Closed]

> Username: aaraia  
> Created at: 2018-07-22 15:58:50 UTC  
> Updated at: 2018-09-20 10:49:04 UTC  
> Closed at: 2018-09-20 10:49:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/501  

I am trying to work out why my code below works as expected in boost 1.61 but not in boost 1.67/1.68.  
  
In boost 1.61 the input polygons are correctly combined and the outline polygon displayed.  
  
In boost 1.67, with no code changes, the outline polygon is wrong and incomplete. Its as if it has missing points.  
  
Output images added to help explain. There is also an #define BOOST_1_6_1 i needed to add as boost 1.61 does not seem to automatically add that header file.  
  
uncomment drawAllPolygons() if you want to see how the input polygons look.  
  
Can anyone help?   
  
```  
#include <iostream>  
  
#include <boost\geometry.hpp>  
  
//#define BOOST_1_6_1  
#ifdef BOOST_1_6_1  
#include <boost/geometry/geometries/point_xy.hpp>  
#endif  
  
typedef boost::geometry::model::d2::point_xy<double> intPt;  
typedef boost::geometry::model::polygon<intPt> polygon;  
  
const int GRID_WIDTH = 220;  
const int GRID_HEIGHT = 60;  
  
bool canvas[GRID_WIDTH][GRID_HEIGHT];  
  
std::vector<polygon> output;  
std::vector<polygon> input;  
  
void initCanvas()  
{  
	for (unsigned int y = 0; y < GRID_HEIGHT; ++y)  
	{  
		for (unsigned int x = 0; x < GRID_WIDTH; ++x)  
		{  
			canvas[x][y] = false;  
		}  
	}  
}  
  
void drawGrid()  
{  
	for (unsigned int y = 0; y < GRID_HEIGHT; ++y)  
	{  
		for (unsigned int x = 0; x < GRID_WIDTH; ++x)  
		{  
			if (canvas[x][y])  
			{  
				std::cout << "x";  
			}   
			else  
			{  
				std::cout << ".";  
			}  
		}  
  
		std::cout << std::endl;  
	}  
}  
  
polygon setupPolygon(const int startX, const int startY, const int width, const int height)  
{  
	polygon polygon1;  
  
	int endX = startX + width;  
	int endY = startY + height;  
  
	for (int x = startX; x <= endX; ++x)  
	{  
		intPt pt(x, startY);  
		polygon1.outer().push_back(pt);  
	}  
  
	for (int y = startY; y <= endY; ++y)  
	{  
		intPt pt(endX, y);  
		polygon1.outer().push_back(pt);  
	}  
  
	for (int x = endX; x >= startX; --x)  
	{  
		intPt pt(x, endY);  
		polygon1.outer().push_back(pt);  
	}  
  
	for (int y = endY; y >= startY; --y)  
	{  
		intPt pt(startX, y);  
		polygon1.outer().push_back(pt);  
	}  
  
	return polygon1;  
}  
  
std::vector<polygon> combine(std::vector<polygon> input)  
{  
	bool loop = true;  
	while (loop)  
	{  
		unsigned int before = input.size();  
		bool exit = false;  
		for (unsigned int i = 0; i < input.size() && !exit; ++i)  
		{  
			for (unsigned int j = i + 1; j < input.size() && !exit; ++j)  
			{  
				std::vector<polygon> output;  
				boost::geometry::correct(input[i]);  
				boost::geometry::correct(input[j]);  
				boost::geometry::union_(input[i], input[j], output);  
  
				if (i < j)  
				{   
					input.erase(input.begin() + j);  
					input.erase(input.begin() + i);  
				}  
				else  
				{  
					input.erase(input.begin() + i);  
					input.erase(input.begin() + j);  
				}  
  
				input.insert(input.begin(), output.begin(), output.end());  
  
				exit = true;  
			}  
		}  
  
		if (before == input.size())  
		{  
			loop = false;  
		}  
	}  
  
  
	return input;  
}  
  
void drawAllPolygons()  
{  
	for (unsigned int i = 0; i < input.size(); ++i)  
	{  
		auto outer = input[i].outer();  
		for (unsigned int i = 0; i < outer.size(); ++i)  
		{  
			int x = outer[i].get<0>();  
			int y = outer[i].get<1>();  
			canvas[x][y] = true;  
		}  
	}  
}  
  
void drawCombinedPolygons()  
{  
	for (unsigned int j = 0; j < output.size(); ++j)  
	{  
		auto outer = output[j].outer();  
		for (unsigned int i = 0; i < outer.size(); ++i)  
		{  
			int x = outer[i].get<0>();  
			int y = outer[i].get<1>();  
  
			canvas[x][y] = true;  
		}  
	}  
}  
  
int main()  
{  
	initCanvas();  
  
	input.push_back(setupPolygon(40, 10, 50, 15));  
	input.push_back(setupPolygon(40, 20, 50, 15));  
	input.push_back(setupPolygon(50, 10, 50, 15));  
	input.push_back(setupPolygon(60, 30, 50, 15));  
  
	output = combine(input);  
  
	//drawAllPolygons();  
	drawCombinedPolygons();  
  
	drawGrid();  
  
	std::cin.get();  
  
	return 0;  
}  
```  
  
  
![boost_161](https://user-images.githubusercontent.com/35140838/43047532-4d8111d2-8dd0-11e8-9287-81f964736b48.png)  
![boost_167](https://user-images.githubusercontent.com/35140838/43047533-4da020ae-8dd0-11e8-8f9b-6dd00766427e.png)

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-08-27 13:44:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/501#issuecomment-416231409  

Please prepare a simpler program without drawing, only with raw data:  
- only Boost.Geometry algorithms called in desired sequence,  
- explicitly defined polygons passed into them (created either with `bg::read_wkt` [1] or initializer lists),  
- the resulting merged polygons in `output` printed with `bg::wkt` [2] one by one, (to simplify you could replace `std::vector<polygon>` with bg::model::multi_polygon<polygon> and print the whole geometry but I guess this could potentially change the result).  
  
where  
  
    namespace bg = boost::geometry;  
  
Models are not included by default by design. If they are included it's only by coincidence, because they were needed internally in some algorithm. Add:  
  
    #include <boost/geometry/geometries/geometries.hpp>  
  
in both versions if you need them.  
  
[1] https://www.boost.org/libs/geometry/doc/html/geometry/reference/io/wkt/read_wkt.html  
[2] https://www.boost.org/libs/geometry/doc/html/geometry/reference/io/wkt/wkt.html

---

## Comment 2

> Username: aaraia  
> Created at: 2018-09-09 18:36:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/501#issuecomment-419735584  

Hi,  
  
I got it fixed in the end through stackoverflow. I was not drawing properly. Boost expects the line between two points to be drawn. I thought it was providing each point that made up the line.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2018-09-20 10:49:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/501#issuecomment-423137757  

Closing issue as per above comment
