# #1059 - "union_" cannot compute the union of two polygons [Closed]

> Username: huntto  
> Created at: 2022-09-22 07:35:28 UTC  
> Updated at: 2022-09-28 07:15:00 UTC  
> Closed at: 2022-09-26 10:35:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1059  

typedef boost::geometry::model::d2::point_xy<double> point;  
typedef boost::geometry::model::polygon<point> polygon;  
  
const double PI = 3.141592653;  
void CreateCircularContour(float cx, float cy, float radius, float resolution, polygon &p)  
{  
    int count = (int)(2 * PI * radius / resolution);  
    float rad_delta = 2 * PI / count;  
    double rad = 0;  
    float fx, fy;  
    for (int i = 0; i < count; i++)  
    {  
        float x = (float)(radius * cos(rad)) + cx;  
        float y = (float)(radius * sin(rad)) + cy;  
        if (i == 0)  
        {  
            fx = x;  
            fy = y;  
        }  
        boost::geometry::append(p.outer(), point(x, y));  
        rad += rad_delta;  
    }  
    boost::geometry::append(p.outer(), point(fx, fy));  
}  
  
...  
  
    polygon subj;  
    CreateCircularContour(200, 200, 50, 2, subj);  
    polygon clip;  
    CreateCircularContour(300, 300, 100, 2, clip);  
  
    std::list<polygon> output;  
    boost::geometry::union_(subj, clip, output);

---

## Comment 1

> Username: barendgehrels  
> Created at: 2022-09-24 13:35:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1059#issuecomment-1256970953  

Hi! What do you mean by: “cannot”? Does it compile? Does it crash? What is the result?  
  
also your code does not look well.  
‘’’  
typedef boost::geometry::model::d2::point_xy point;  
typedef boost::geometry::model::polygon polygon  
‘’’  
Where is the type? Double? Is it clockwise?

---

## Comment 2

> Username: huntto  
> Created at: 2022-09-26 10:34:33 UTC  
> Updated at: 2022-09-26 10:46:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1059#issuecomment-1257830931  

> Hi! What do you mean by: “cannot”? Does it compile? Does it crash? What is the result?  
>   
> also your code does not look well. ‘’’ typedef boost::geometry::model::d2::point_xy point; typedef boost::geometry::model::polygon polygon ‘’’ Where is the type? Double? Is it clockwise?  
  
Thanks for your reply, by changing counterclockwise to clockwise  I got a correct polygon instead of an empty one.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2022-09-28 07:15:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/1059#issuecomment-1260486143  

Good it's solved.  
  
The safest way is to call `boost::geometry::correct` on both polygons before calling `union_`. Then you know the orientation and the closure are right.
