# #1245 - [Question] Get edges intersected with bounding boxes using boost::geometry::index::intersects [Closed]

> Username: bootsmakes  
> Created at: 2024-02-23 16:55:48 UTC  
> Updated at: 2024-02-24 12:01:54 UTC  
> Closed at: 2024-02-24 12:01:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1245  

Hello, I'm trying to get edges intersected with bounding boxes using `boost::geometry::index::intersects`, but the result is always missing some edges. Here's the code to reproduce.  
```  
  
#include <iostream>  
#include <igl/readOBJ.h>  
#include <igl/edge_topology.h>  
#include <boost/geometry.hpp>  
#include <boost/geometry/index/rtree.hpp>  
   
int main()  
{  
    Eigen::MatrixXd vertices;  
    Eigen::MatrixXi faces,EV, FE, EF;  
    igl::readOBJ("../data/test.obj", vertices, faces);  
    double radius = 0.5;  
    igl::edge_topology(vertices, faces, EV, FE, EF);  
   
    Eigen::VectorXd angles(EV.rows());  
    std::vector<bool> face_adjacency_convex(EF.rows());  
       
    typedef boost::geometry::model::point<float, 3, boost::geometry::cs::cartesian> point;  
    typedef boost::geometry::model::box<point> box;  
    typedef boost::geometry::index::rtree< std::pair<box, size_t>, boost::geometry::index::quadratic<16> > rtree;  
    rtree tree;  
       
    for (int eid = 0; eid < EV.rows(); ++eid) {  
        auto v1 = vertices.row(EV(eid, 0));  
        auto v2 = vertices.row(EV(eid, 1));   
        point p1(v1[0], v1[1], v1[2]), p2(v2[0], v2[1], v2[2]);  
        box edge_box(p1, p2);  
        tree.insert(std::make_pair(edge_box, eid));  
    }  
       
    for (int i = 0; i < vertices.rows(); i++)  
    {  
        std::cout<<"vertex:"<<i<<std::endl;  
   
        Eigen::RowVector3d x = vertices.row(i);  
        Eigen::RowVector3d bbox_min = x.array() - radius;  
        Eigen::RowVector3d bbox_max = x.array() + radius;  
  
        point min_point(bbox_min[0], bbox_min[1], bbox_min[2]);  
        point max_point(bbox_max[0], bbox_max[1], bbox_max[2]);  
           
        box query_box(min_point, max_point);  
        std::vector<std::pair<box, size_t>> query_result;  
           
        // Query the tree for intersecting boxes  
        std::vector<int> x_candidates;  
        tree.query(boost::geometry::index::intersects(query_box), std::back_inserter(query_result));  
        for (const auto& item : query_result) {  
            size_t intersect_eid = item.second;  
            x_candidates.push_back(intersect_eid);  
            std::cout<<"\t  x_candidates:("<<EV(intersect_eid,0)<<","<<EV(intersect_eid,1)<<")"<<std::endl;  
        }  
    }  
}  
```  
  
What am I doing wrong here?

---

## Comment 1

> Username: awulkiew  
> Created at: 2024-02-23 20:23:45 UTC  
> Updated at: 2024-02-24 00:20:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1245#issuecomment-1961940791  

The code looks good on a first glance. I won't be able to reproduce it because data is loaded from file.  
  
How do you know that some edges are missing?  
  
To check whether or not it's an issue with the rtree and not e.g. with data you could replace the rtree with `std::vector` holding exactly the same elements and `rtree.query()` with searching for all elements which `boost::geometry::intersects(elem.first, query_box)` returns `true` for.

---

## Comment 2

> Username: bootsmakes  
> Created at: 2024-02-24 02:56:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1245#issuecomment-1962227747  

@awulkiew Thanks. I changed   
```  
        point p1(v1[0], v1[1], v1[2]), p2(v2[0], v2[1], v2[2]);  
        box edge_box(p1, p2);  
```  
to   
```  
    point min_point(std::min(v1[0], v2[0]), std::min(v1[1], v2[1]), std::min(v1[2], v2[2]));  
    point max_point(std::max(v1[0], v2[0]), std::max(v1[1], v2[1]), std::max(v1[2], v2[2]));  
    box edge_box(min_point, max_point);   
```  
the result is now correct.
