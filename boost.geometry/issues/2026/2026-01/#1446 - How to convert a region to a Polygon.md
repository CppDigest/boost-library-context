# #1446 - How to convert a region to a Polygon? [Closed]

> Username: Julien-Livet  
> Created at: 2026-01-18 14:13:48 UTC  
> Updated at: 2026-01-19 18:32:34 UTC  
> Closed at: 2026-01-19 18:32:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1446  

I have a matrix and I can extract color regions as std::vector<std::pair<int, int> > and I want to convert them into Polygon Boost objects. I have yet no idea how to proceed. Here is a matrix example with a region of 3 and 0 and the matching searched polygon.  
<img width="229" height="239" alt="Image" src="https://github.com/user-attachments/assets/3020a2e1-183c-4abc-8b6d-cf1f4c681ee5" />  
  
Here is a sample code:  
```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
  
#include <Eigen/Core>  
  
template <typename Matrix>  
bool validIndex(Matrix const& a, std::pair<int, int> const& at)  
{  
    return (0 <= at.first && at.first < a.rows() && 0 <= at.second && at.second < a.cols());  
}  
  
inline std::vector<std::pair<int, int> > neighbors(std::pair<int, int> const& loc, std::pair<int, int> const& size, bool diagonals)  
{  
    std::vector<std::pair<int, int> > n;  
  
    for (int di = -1; di <= 1; ++di)  
    {  
        for (int dj = -1; dj <= 1; ++dj)  
        {  
            if (!di && !dj)  
                continue;  
  
            auto const i{loc.first + di};  
            auto const j{loc.second + dj};  
  
            if (!(0 <= i && i < size.first && 0 <= j && j < size.second))  
                continue;  
  
            if (!diagonals && std::abs(di) == std::abs(dj))  
                continue;  
  
            n.emplace_back(std::make_pair(i, j));  
        }  
    }  
  
    return n;  
}  
  
template <typename Matrix>  
std::vector<std::pair<int, int> > region(Matrix const& a, std::pair<int, int> const& at, bool diagonals)  
{  
    if (!validIndex(a, at))  
        return std::vector<std::pair<int, int> >{};  
  
    std::set<std::pair<int, int> > s;  
    std::set<std::pair<int, int> > stack;  
    stack.emplace(at);  
    auto const v{a(at.first, at.second)};  
    std::vector<std::pair<int, int> > indices;  
  
    while (stack.size())  
    {  
        auto const loc{*stack.begin()};  
        stack.erase(stack.begin());  
  
        if (s.find(loc) == s.end())  
        {  
            s.emplace(loc);  
  
            if (std::abs(a(loc.first, loc.second) - v) < 1.0e-6)  
            {  
                indices.emplace_back(loc);  
  
                for (auto const& n : neighbors(loc, std::make_pair((int)a.rows(), (int)a.cols()), diagonals))  
                    stack.emplace(n);  
            }  
        }  
    }  
  
    return indices;  
}  
  
template <typename Matrix>  
std::set<std::vector<std::pair<int, int> > > regionSet(Matrix const& m, bool diagonals)  
{  
    std::set<std::vector<std::pair<int, int> > > s;  
  
    for (int i{0}; i < m.rows(); ++i)  
    {  
        for (int j{0}; j < m.cols(); ++j)  
        {  
            auto r{region(m, std::make_pair(i, j), diagonals)};  
  
            std::sort(r.begin(), r.end(),  
                      [] (auto const& x, auto const& y) -> bool  
                      {  
                          if (x.first == y.first)  
                              return x.second < y.second;  
  
                          return x.first < y.first;  
                      });  
  
            if (s.find(r) == s.end())  
                s.emplace(r);  
        }  
    }  
  
    return s;  
}  
  
using Point = boost::geometry::model::d2::point_xy<int>;  
using Polygon = boost::geometry::model::polygon<Point>;  
  
Polygon regionToPolygon(std::vector<std::pair<int, int> > const& region)  
{  
    //...  
}  
  
int main()  
{  
    Eigen::MatrixXi input(10, 10);  
  
    input << 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
             0, 3, 3, 3, 3, 3, 3, 3, 3, 0,  
             0, 3, 3, 0, 0, 3, 0, 0, 3, 0,  
             0, 3, 3, 0, 0, 3, 3, 3, 3, 0,  
             0, 3, 3, 3, 3, 3, 3, 3, 3, 0,  
             0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
             0, 0, 0, 0, 0, 0, 0, 3, 3, 0,  
             0, 0, 3, 3, 3, 0, 0, 3, 0, 3,  
             0, 0, 3, 3, 0, 3, 0, 0, 3, 0,  
             0, 0, 0, 0, 3, 0, 0, 0, 0, 0;  
  
    auto const s{regionSet(input, false)};  
  
    std::map<int, std::vector<std::vector<std::pair<int, int> > > > regions;  
  
    for (int i{0}; i < 10; ++i)  
        regions[i].clear();  
  
    for (auto const& r : s)  
        regions[input(r.front().first, r.front().second)].emplace_back(r);  
  
    auto const polygon1{regionToPolygon(regions.at(3)[0])};  
    auto const polygon2{regionToPolygon(regions.at(3)[1])};  
  
    return 0;  
}  
```

---

## Comment 1

> Username: barendgehrels  
> Created at: 2026-01-18 16:32:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1446#issuecomment-3765469937  

As discussed here: https://stackoverflow.com/questions/79869958/how-to-convert-a-region-to-a-polygon

---

## Comment 2

> Username: barendgehrels  
> Created at: 2026-01-18 16:44:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1446#issuecomment-3765482196  

This should do the job:  
  
```cpp  
Polygon regionToPolygon(std::vector<std::pair<int, int> > const& region)  
{  
    if (region.empty()) {  
        // Sanity check  
        return Polygon{};  
    }  
  
    // A "Ring" is a polygon without holes  
    using Ring = boost::geometry::model::ring<Point>;  
  
    // Boost.Geometry's union always results in a multi-polygons  
    using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
  
    // This will be the result  
    MultiPolygon multi_polygon;  
    for (auto const& loc : region)  
    {  
        // Construct a ring, clockwise, up to the next coordinate  
        // assuming the cell extends to its lower right:  
        auto const& x = loc.first;  
        auto const& y = loc.second;  
  
        const Ring ring = {{x, y}, {x, y + 1}, {x + 1, y + 1}, {x + 1, y}, {x, y}};  
  
        if (multi_polygon.empty()) {  
            // Add the first ring as the first polygon, without interior rings (holes).  
            multi_polygon.push_back(Polygon{ ring, {} });  
            continue;  
        }     
  
        // Combine the result with the added cell (ring)  
        boost::geometry::model::multi_polygon<Polygon> combined;  
        boost::geometry::union_(multi_polygon, ring, combined);  
        multi_polygon = std::move(combined);  
    }  
  
    // Optionally, simplify the result, changing sequences like (5,1), (4,1), (3,1) into (5,1), (3,1)  
    constexpr bool do_simplify = true;  
    if (do_simplify)  
    {  
        MultiPolygon simplified;  
        boost::geometry::simplify(multi_polygon, simplified, 0);   
        multi_polygon = std::move(simplified);  
    }  
  
    // Sanity check, the area should equal the input region  
    // There should just be one polygon (if the neighbor check is right and diagonals are not allowed)  
    // If diagonals are allowed, there can be more polygons and you will have to return a multipolygon instead  
    std::cout   
        << "input count: " << region.size()   
        << "  output area: " << boost::geometry::area(multi_polygon)   
        << "  output count: " << multi_polygon.size()  
        << "  output points: " << boost::geometry::num_points(multi_polygon)   
        << std::endl;  
  
    // Debug output showing its coordinates  
    std::cout << "  " << boost::geometry::dsv(multi_polygon) << std::endl;  
  
    // As mentioned before, you can also return a multi polygon.  
    // The resulting multi polygon should never be empty, it is checked at the start.  
    return multi_polygon.front();  
}  
```  
  
When called with:  
```cpp  
    auto const polygon1{regionToPolygon(regions.at(3)[0])};  
    auto const polygon2{regionToPolygon(regions.at(3)[1])};  
    auto const polygon3{regionToPolygon(regions.at(3)[2])};  
```  
  
These gives the following output:  
```  
input count: 26  output area: 26  output count: 1  output points: 15  
  ((((1, 1), (1, 9), (5, 9), (5, 1), (1, 1)), ((2, 6), (3, 6), (3, 8), (2, 8), (2, 6)), ((2, 3), (4, 3), (4, 5), (2, 5), (2, 3))))  
input count: 3  output area: 3  output count: 1  output points: 7  
  ((((8, 7), (6, 7), (6, 9), (7, 9), (7, 8), (8, 8), (8, 7))))  
input count: 5  output area: 5  output count: 1  output points: 7  
  ((((7, 5), (8, 5), (8, 4), (9, 4), (9, 2), (7, 2), (7, 5))))  
```

---

## Comment 3

> Username: Julien-Livet  
> Created at: 2026-01-19 09:44:19 UTC  
> Updated at: 2026-01-19 15:45:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1446#issuecomment-3767388860  

Thanks for your answer. I modified the code to consider a region with one point as a polygon. I am searching how to improve the code because the square region of 0 has not the right polygon (the right polygon should have the same point of the region but reordered):  
```bash  
0 (2, 3), (2, 4), (3, 3), (3, 4),   
(((2, 5), (4, 5), (4, 3), (2, 3), (2, 5)))  
```  
My final goal is to extract the enclosed regions, the three regions of 3 and the four regions of 0.  
  
Here is my current code:  
```cpp  
#include <deque>  
#include <iostream>  
#include <map>  
#include <set>  
#include <vector>  
  
#include <Eigen/Core>  
  
template <typename Matrix>  
bool validIndex(Matrix const& a, std::pair<int, int> const& at)  
{  
    return (0 <= at.first && at.first < a.rows() && 0 <= at.second && at.second < a.cols());  
}  
  
inline std::vector<std::pair<int, int> > neighbors(std::pair<int, int> const& loc, std::pair<int, int> const& size, bool diagonals)  
{  
    std::vector<std::pair<int, int> > n;  
  
    for (int di = -1; di <= 1; ++di)  
    {  
        for (int dj = -1; dj <= 1; ++dj)  
        {  
            if (!di && !dj)  
                continue;  
  
            auto const i{loc.first + di};  
            auto const j{loc.second + dj};  
  
            if (!(0 <= i && i < size.first && 0 <= j && j < size.second))  
                continue;  
  
            if (!diagonals && std::abs(di) == std::abs(dj))  
                continue;  
  
            n.emplace_back(std::make_pair(i, j));  
        }  
    }  
  
    return n;  
}  
  
template <typename Matrix>  
std::vector<std::pair<int, int> > region(Matrix const& a, std::pair<int, int> const& at, bool diagonals)  
{  
    if (!validIndex(a, at))  
        return std::vector<std::pair<int, int> >{};  
  
    std::set<std::pair<int, int> > s;  
    std::set<std::pair<int, int> > stack;  
    stack.emplace(at);  
    auto const v{a(at.first, at.second)};  
    std::vector<std::pair<int, int> > indices;  
  
    while (stack.size())  
    {  
        auto const loc{*stack.begin()};  
        stack.erase(stack.begin());  
  
        if (s.find(loc) == s.end())  
        {  
            s.emplace(loc);  
  
            if (std::abs(a(loc.first, loc.second) - v) < 1.0e-6)  
            {  
                indices.emplace_back(loc);  
  
                for (auto const& n : neighbors(loc, std::make_pair((int)a.rows(), (int)a.cols()), diagonals))  
                    stack.emplace(n);  
            }  
        }  
    }  
  
    return indices;  
}  
  
template <typename Matrix>  
std::set<std::vector<std::pair<int, int> > > regionSet(Matrix const& m, bool diagonals)  
{  
    std::set<std::vector<std::pair<int, int> > > s;  
  
    for (int i{0}; i < m.rows(); ++i)  
    {  
        for (int j{0}; j < m.cols(); ++j)  
        {  
            auto r{region(m, std::make_pair(i, j), diagonals)};  
  
            std::sort(r.begin(), r.end(),  
                      [] (auto const& x, auto const& y) -> bool  
                      {  
                          if (x.first == y.first)  
                              return x.second < y.second;  
  
                          return x.first < y.first;  
                      });  
  
            if (s.find(r) == s.end())  
                s.emplace(r);  
        }  
    }  
  
    return s;  
}  
  
struct Point  
{  
    int x;  
    int y;  
  
    bool operator==(Point const& other) const  
    {  
        return x == other.x && y == other.y;  
    }  
  
    bool operator<(Point const& other) const  
    {  
        return std::tie(x, y) < std::tie(other.x, other.y);  
    }  
};  
  
struct Edge  
{  
    Point a;  
    Point b;  
  
    Edge(Point const& p1, Point const& p2)  
    {  
        a = p1;  
        b = p2;  
  
        if (a < b)  
            std::swap(a, b);  
    }  
  
    bool operator<(Edge const& other) const  
    {  
        return std::tie(a, b) < std::tie(other.a, other.b);  
    }  
};  
  
std::vector<Edge> extractBoundaryEdges(std::vector<std::pair<int, int> > const& region)  
{  
    std::map<Edge, int> edgeCount;  
  
    for (auto const& p : region)  
    {  
        auto const& x{p.first};  
        auto const& y{p.second};  
  
        Point p00{x, y};  
        Point p10{x + 1, y};  
        Point p11{x + 1, y + 1};  
        Point p01{x, y + 1};  
  
        ++edgeCount[Edge(p00, p10)];  
        ++edgeCount[Edge(p10, p11)];  
        ++edgeCount[Edge(p11, p01)];  
        ++edgeCount[Edge(p01, p00)];  
    }  
  
    std::vector<Edge> boundary;  
  
    for (auto const& [edge, count] : edgeCount)  
    {  
        if (count == 1)  
            boundary.emplace_back(edge);  
    }  
  
    return boundary;  
}  
  
using Polygon = std::vector<Point>;  
  
std::vector<Polygon> boundaryToPolygons(std::vector<Edge> const& boundary)  
{  
    if (boundary.empty())  
        return std::vector<Polygon>{};  
  
    std::vector<std::set<Point> > points;  
    std::vector<std::set<Edge> > edges;  
  
    for (auto const& e : boundary)  
    {  
        points.emplace_back(std::set<Point>{e.a, e.b});  
        edges.emplace_back(std::set<Edge>{e});  
    }  
  
    bool modified{true};  
  
    while (modified)  
    {  
        modified = false;  
  
        for (size_t i{0}; i < edges.size(); ++i)  
        {  
            for (size_t j{i + 1}; j < edges.size(); ++j)  
            {  
                for (auto const& point : points[j])  
                {  
                    if (points[i].find(point) != points[i].end())  
                    {  
                        modified = true;  
                        points[i].insert(points[j].begin(), points[j].end());  
                        edges[i].insert(edges[j].begin(), edges[j].end());  
  
                        points[j] = points.back();  
                        points.pop_back();  
                        edges[j] = edges.back();  
                        edges.pop_back();  
  
                        break;  
                    }  
                }  
  
                if (modified)  
                    break;  
            }  
  
            if (modified)  
                break;  
        }  
    }  
  
    std::vector<std::deque<Edge> > orderedEdges;  
    orderedEdges.resize(edges.size());  
  
    for (size_t i{0}; i < edges.size(); ++i)  
    {  
        orderedEdges[i].emplace_back(*edges[i].begin());  
        edges[i].erase(edges[i].begin());  
    }  
  
    for (size_t i{0}; i < edges.size(); ++i)  
    {  
        std::vector<Edge> copiedEdges{edges[i].begin(), edges[i].end()};  
  
        for (size_t j{0}; j < copiedEdges.size(); ++j)  
        {  
            auto const& e{copiedEdges[j]};  
            bool remove{false};  
  
            if (orderedEdges[i].front().a == e.a & orderedEdges[i].front().a == e.b  
                || orderedEdges[i].front().b == e.a || orderedEdges[i].front().b == e.b)  
            {  
                orderedEdges[i].emplace_front(e);  
                remove = true;  
            }  
            else if (orderedEdges[i].back().a == e.a || orderedEdges[i].back().a == e.b  
                || orderedEdges[i].back().b == e.a || orderedEdges[i].back().b == e.b)  
            {  
                orderedEdges[i].emplace_back(e);  
                remove = true;  
            }  
  
            if (remove)  
            {  
                copiedEdges[j] = copiedEdges.back();  
                copiedEdges.pop_back();  
                j = 0;  
                --j;  
            }  
        }  
    }  
  
    std::vector<std::deque<Point> > orderedPolygons;  
    orderedPolygons.resize(orderedEdges.size());  
  
    for (size_t i{0}; i < orderedEdges.size(); ++i)  
    {  
        orderedPolygons[i].emplace_back(orderedEdges[i].front().a);  
        orderedPolygons[i].emplace_back(orderedEdges[i].front().b);  
    }  
  
    for (size_t i{0}; i < orderedEdges.size(); ++i)  
    {  
        for (size_t j{1}; j < orderedEdges[i].size(); ++j)  
        {  
            if (orderedPolygons[i].back() == orderedEdges[i][j].a)  
                orderedPolygons[i].emplace_back(orderedEdges[i][j].b);  
            else if (orderedPolygons[i].back() == orderedEdges[i][j].b)  
                orderedPolygons[i].emplace_back(orderedEdges[i][j].a);  
            else if (orderedPolygons[i].front() == orderedEdges[i][j].b)  
                orderedPolygons[i].emplace_front(orderedEdges[i][j].a);  
            else if (orderedPolygons[i].front() == orderedEdges[i][j].a)  
                orderedPolygons[i].emplace_front(orderedEdges[i][j].b);  
        }  
    }  
  
    std::vector<Polygon> polygons;  
    polygons.reserve(orderedPolygons.size());  
  
    for (auto const& polygon : orderedPolygons)  
        polygons.emplace_back(polygon.begin(), polygon.end());  
  
    std::sort(polygons.begin(), polygons.end(), [] (auto const& a, auto const& b) { return a.size() < b.size(); });  
  
    return polygons;  
}  
  
int main()  
{  
    Eigen::MatrixXi input(10, 10);  
  
    input << 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
             0, 3, 3, 3, 3, 3, 3, 3, 3, 0,  
             0, 3, 3, 0, 0, 3, 0, 0, 3, 0,  
             0, 3, 3, 0, 0, 3, 3, 3, 3, 0,  
             0, 3, 3, 3, 3, 3, 3, 3, 3, 0,  
             0, 0, 0, 0, 0, 0, 0, 0, 0, 0,  
             0, 0, 0, 0, 0, 0, 0, 3, 3, 0,  
             0, 0, 3, 3, 3, 0, 0, 3, 0, 3,  
             0, 0, 3, 3, 0, 3, 0, 0, 3, 0,  
             0, 0, 0, 0, 3, 0, 0, 0, 0, 0;  
  
    Polygon backgroudPolygon;  
    std::vector<std::pair<int, int> > backgroudRegion;  
  
    {  
        auto const s{regionSet(input, false)};  
  
        std::map<int, std::vector<std::vector<std::pair<int, int> > > > regionMap;  
  
        for (int i{0}; i < 10; ++i)  
            regionMap[i].clear();  
  
        std::vector<Polygon> polygons;  
        std::vector<std::vector<std::pair<int, int> > > regions;  
  
        for (auto const& r : s)  
        {  
            regionMap[input(r.front().first, r.front().second)].emplace_back(r);  
  
            auto const newPolygons{boundaryToPolygons(extractBoundaryEdges(r))};  
  
            polygons.insert(polygons.begin(), newPolygons.begin(), newPolygons.end());  
            regions.emplace_back(r);  
        }  
  
        std::sort(polygons.begin(), polygons.end(), [] (auto const& a, auto const& b) { return a.size() < b.size(); });  
        std::sort(regions.begin(), regions.end(), [] (auto const& a, auto const& b) { return a.size() < b.size(); });  
  
        backgroudPolygon = polygons.back();  
        backgroudRegion = regions.back();  
    }  
  
    {  
        auto m{input};  
  
        for (int i{0}; i < m.rows(); ++i)  
        {  
            for (int j{0}; j < m.cols(); ++j)  
            {  
                if (std::find(backgroudRegion.begin(), backgroudRegion.end(), std::make_pair(i, j)) != backgroudRegion.end())  
                    m(i, j) = input.maxCoeff() + 1;  
            }  
        }  
  
        auto const s{regionSet(input, true)};  
  
        std::map<int, std::vector<std::vector<std::pair<int, int> > > > regionMap;  
  
        for (int i{0}; i < 11; ++i)  
            regionMap[i].clear();  
  
        std::vector<Polygon> polygons;  
        std::vector<std::vector<std::pair<int, int> > > regions;  
  
        for (auto const& r : s)  
        {  
            regionMap[input(r.front().first, r.front().second)].emplace_back(r);  
  
            auto const newPolygons{boundaryToPolygons(extractBoundaryEdges(r))};  
  
            polygons.insert(polygons.begin(), newPolygons.begin(), newPolygons.end());  
            regions.emplace_back(r);  
        }  
  
        regionMap[m.maxCoeff()].clear();  
  
        std::sort(polygons.begin(), polygons.end(), [] (auto const& a, auto const& b) { return a.size() < b.size(); });  
    }  
  
    return 0;  
}  
```

---

## Comment 4

> Username: barendgehrels  
> Created at: 2026-01-19 17:22:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/1446#issuecomment-3769457255  

> I modified the code to consider a region with one point as a polygon.   
  
That's completely fine of course. But as you're now have the algorithm without, and are not using Boost.Geometry anymore, you can close this issue.
