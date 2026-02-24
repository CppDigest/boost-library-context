# #6 - Missing include mat.hpp: qvm assert is not included [Closed]

> Username: nightsparc  
> Created at: 2017-04-28 12:18:39 UTC  
> Updated at: 2017-05-14 09:42:25 UTC  
> Closed at: 2017-05-14 09:42:25 UTC  
> Url: https://github.com/boostorg/qvm/issues/6  

Hi,  
  
the mat.hpp is missing the mat_operations.hpp as a dependent include.  
  
Thus, our code which is heavily relying on boost::geometry fails to build with boost 1.64 - where the geometry authors changed their matrix_transformers.hpp to use qvm instead of ublas.  
  
```  
/usr/local/include/boost/qvm/mat.hpp:29: error: expected primary-expression before ‘(’ token  
                 assign(r,*this);  
                       ^  
```  
Basically, in line 29 of the qvm/mat.hpp `assert` is called:  
  
```c++  
template <class R>  
operator R() const  
    {  
    R r;  
    assign(r,*this); // not recognized  
    return r;  
    }  
```  
which is not available here as the mat_operations.hpp is missing.  
  
This seems to be working when using the qvm standalone (at least in my small tests here) but clashes in conjunction with boost::geometry, where the mat.hpp is included prior the mat_operations.hpp:  
  
```c++  
// file: /usr/local/include/boost/geometry/strategies/transform/matrix_transformers.hpp  
  
#include <boost/qvm/mat.hpp>  
#include <boost/qvm/mat_access.hpp>  
#include <boost/qvm/mat_operations.hpp> // should be included prior to the qvm/mat.hpp  
```  
I'm not sure if this behaviours is intended or not...but I think it's just a missing include in the qvm mat.hpp :)  
  
For us here, I patched the mat.hpp to include mat_operations.hpp as well - seems to work in our setup here...  
  
```c++  
...  
  
#ifndef UUID_67E67D68A32F11DEA56FD18556D89593  
#define UUID_67E67D68A32F11DEA56FD18556D89593  
  
#include <boost/qvm/inline.hpp>  
#include <boost/qvm/assert.hpp>  
#include <boost/qvm/static_assert.hpp>  
#include <boost/qvm/mat_operations.hpp>  
  
namespace  
boost  
    {  
    namespace  
    qvm  
        {  
        template <class T,int Rows,int Cols>  
        struct  
        mat  
            {  
            T a[Rows][Cols];  
            template <class R>  
            operator R() const  
                {  
                R r;  
                assign(r,*this);  
                return r;  
                }  
            };  
...  
```  
  
Oh, just for completeness: We're using Ubuntu 16.04 with the system GCC 5.4

---

## Comment 1

> Username: zajo  
> Created at: 2017-04-28 20:33:28 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-298099853  

May I please see a complete (hopefully simple) cpp file that demonstrates  
the problem? Which boost::geometry header do you include, what  
boost::geometry function do you call (so I know what templates get  
instantiated.)  
  
Thanks,  
Emil  
  
On Fri, Apr 28, 2017 at 5:18 AM, nightsparc <notifications@github.com>  
wrote:  
  
> Hi,  
>  
> the mat.hpp is missing the mat_operations.hpp as a dependent include.  
>  
> Thus, our code which is heavily relying on boost::geometry fails to build  
> with boost 1.64 - where the geometry authors changed their  
> matrix_transformers.hpp to use qvm instead of ublas.  
>  
> /usr/local/include/boost/qvm/mat.hpp:29: error: expected primary-expression before ‘(’ token  
>                  assign(r,*this);  
>                        ^  
>  
> Basically, in line 29 of the qvm/mat.hpp assert is called:  
>  
> template <class R>operator R() const  
>     {  
>     R r;  
>     assign(r,*this); // not recognized  
>     return r;  
>     }  
>  
> which is not available here as the mat_operations.hpp is missing.  
>  
> This seems to be working when using the qvm standalone (at least in my  
> small tests here) but clashes in conjunction with boost::geometry, where  
> the mat.hpp is included prior the mat_operations.hpp:  
>  
> // file: /usr/local/include/boost/geometry/strategies/transform/matrix_transformers.hpp  
>  
> #include <boost/qvm/mat.hpp>  
> #include <boost/qvm/mat_access.hpp>  
> #include <boost/qvm/mat_operations.hpp> // should be included prior to the qvm/mat.hpp  
>  
> I'm not sure if this behaviours is intended or not...but I think it's just  
> a missing include in the qvm mat.hpp :)  
>  
> For us here, I patched the mat.hpp to include mat_operations.hpp as well -  
> seems to work in our setup here...  
>  
> ...  
>  
> #ifndef UUID_67E67D68A32F11DEA56FD18556D89593  
> #define UUID_67E67D68A32F11DEA56FD18556D89593  
>  
> #include <boost/qvm/inline.hpp>  
> #include <boost/qvm/assert.hpp>  
> #include <boost/qvm/static_assert.hpp>  
> #include <boost/qvm/mat_operations.hpp>  
> namespace  
> boost  
>     {  
>     namespace  
>     qvm  
>         {  
>         template <class T,int Rows,int Cols>  
>         struct  
>         mat  
>             {  
>             T a[Rows][Cols];  
>             template <class R>  
>             operator R() const  
>                 {  
>                 R r;  
>                 assign(r,*this);  
>                 return r;  
>                 }  
>             };  
> ...  
>  
> Oh, just for completeness: We're using Ubuntu 16.04 with the system GCC 5.4  
>

---

## Comment 2

> Username: aaronovz1  
> Created at: 2017-05-12 17:39:23 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-301140075  

I see the same problem on 16.04 with GCC 5.4 running boost 1.64. The includes we are using are:  
```  
#include <boost/geometry/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/multi/geometries/multi_geometries.hpp>  
#include <boost/geometry/geometries/register/point.hpp>  
```

---

## Comment 3

> Username: zajo  
> Created at: 2017-05-12 22:28:34 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-301199973  

On Fri, May 12, 2017 at 10:39 AM, Aaron <notifications@github.com> wrote:  
  
> I see the same problem on 16.04 with GCC 5.4 running boost 1.64. The  
> includes we are using are:  
>  
> #include <boost/geometry/geometry.hpp>  
> #include <boost/geometry/geometries/geometries.hpp>  
> #include <boost/geometry/geometries/point_xy.hpp>  
> #include <boost/geometry/multi/geometries/multi_geometries.hpp>  
> #include <boost/geometry/geometries/register/point.hpp>  
>  
>  
Are you saying that these includes fail to compile? I doubt that very much  
because there are unit tests in QVM to verify that each header compiles  
when included on its own, but do let me know if that is not the case.  
Otherwise can I please have the actual code that demonstrates the error  
you're getting?  
  
Thanks!  
Emil

---

## Comment 4

> Username: aaronovz1  
> Created at: 2017-05-12 22:39:55 UTC  
> Updated at: 2017-05-12 22:44:01 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-301201615  

I am saying that order of includes causes the problem. There is far too much code to show and the errors out of GCC don't point to a particular line of code per se, it points to the #include of boost/geometry/geometry.hpp.  
  
I think it is like @nightsparc stated:  
  
> clashes in conjunction with boost::geometry, where the mat.hpp is included prior the mat_operations.hpp  
  
Snippet from GCC:  
  
> 10:29:38.294 In file included from ../../..//boost1.64.0/include/boost/geometry/strategies/transform/matrix_transformers.hpp:25:0,  
> 10:29:38.294                  from ../../../boost1.64.0/include/boost/geometry/strategies/strategies.hpp:104,  
> 10:29:38.294                  from ../../../boost1.64.0/include/boost/geometry/geometry.hpp:50,  
> 10:29:38.294                  from ../~/computation/geom/boost_geometry_include.h:14,  
> 10:29:38.295                  from ../testcase.cpp:410:  
> 10:29:38.295 ../../../boost1.64.0/include/boost/qvm/mat.hpp: In member function 'boost::qvm::mat<T, Rows, Cols>::operator R() const':  
> 10:29:38.295 ../../../boost1.64.0/include/boost/qvm/mat.hpp:28:23: error: expected primary-expression before '(' token  
> 10:29:38.295                  assign(r,*this);

---

## Comment 5

> Username: nightsparc  
> Created at: 2017-05-13 22:15:27 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-301278152  

@zajo   
  
Sorry for the late reply. I was a bit busy the last week.  
Ah, the working example is quite simple ;)  
```C++  
#include <iostream>  
  
// induces unintended ADL: boost::assign namespace  
#include <boost/assign/list_of.hpp>  
  
//#include <boost/qvm/mat_operations.hpp> // uncomment to get it working...  
#include <boost/qvm/mat.hpp>  
  
using namespace std;  
  
int main(int argc, char *argv[])  
{  
    cout << "Hello World!" << endl;  
    return 0;  
}  
```  
But to be honest, it took some time to break it down...too much code, just as @aaronovz1 mentioned. Anyway, I was able to track it down :) it looks like you've got an unintended ADL problem here (as far as I understand the standard here...), when combining boost::qvm with boost::assign.   
Have a look at line 28 of qvms ``mat.hpp``:  
  
```C++  
template <class T,int Rows,int Cols>  
struct  
mat  
    {  
    T a[Rows][Cols];  
    template <class R>  
    operator R() const  
        {  
        R r;  
        // Line 28  
        assign(r,*this); // thanks to ADL, assign is the boost::assign namespace...  
        return r;  
        }  
    };  
```  
  
Assuming that you would use the qvm::assign here, I would propose to use the fully qualified name for the assign call AND to include the `mat_operators.hpp` above...something like this:  
  
```C++  
...  
#include <boost/qvm/inline.hpp>  
#include <boost/qvm/assert.hpp>  
#include <boost/qvm/static_assert.hpp>  
#include <boost/qvm/mat_operations.hpp>  
  
namespace  
boost  
    {  
    namespace  
    qvm  
        {  
        template <class T,int Rows,int Cols>  
        struct  
        mat  
            {  
            T a[Rows][Cols];  
            template <class R>  
            operator R() const  
                {  
                R r;  
                boost::qvm::assign(r,*this);  
                return r;  
                }  
            };  
...  
```  
  
Or did I miss something and the unqualified name usage was intended?

---

## Comment 6

> Username: zajo  
> Created at: 2017-05-14 04:32:11 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-301290372  

On Sat, May 13, 2017 at 3:15 PM, nightsparc <notifications@github.com>  
wrote:  
  
> @zajo <https://github.com/zajo>  
>  
> Sorry for the late reply. I was a bit busy the last week.  
> Ah, the working example is quite simple ;)  
>  
> #include <iostream>  
> // induces unintended ADL: boost::assign namespace  
> #include <boost/assign/list_of.hpp>  
> //#include <boost/qvm/mat_operations.hpp> // uncomment to get it working...  
> #include <boost/qvm/mat.hpp>  
> using namespace std;  
> int main(int argc, char *argv[])  
> {  
>     cout << "Hello World!" << endl;  
>     return 0;  
> }  
>  
> But to be honest, it took some time to break it down...too much code, just  
> as @aaronovz1 <https://github.com/aaronovz1> mentioned. Anyway, I was  
> able to track it down :)  
>  
Thank you for tracking this down. It should be fixed, please pull develop  
to confirm and let me know so I can push the fix into master as well.  
  
Emil

---

## Comment 7

> Username: nightsparc  
> Created at: 2017-05-14 09:42:25 UTC  
> Url: https://github.com/boostorg/qvm/issues/6#issuecomment-301301411  

@zajo   
Works for me :)   
I've tested develop against our codebase...so, with the next boost release I'll be removing my internal workaround :)
