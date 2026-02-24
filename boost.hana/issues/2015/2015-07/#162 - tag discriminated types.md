# #162 - tag discriminated types? [Closed]

> Username: ahundt  
> Created at: 2015-07-09 21:48:21 UTC  
> Updated at: 2015-08-26 12:09:52 UTC  
> Closed at: 2015-08-18 20:01:46 UTC  
> Url: https://github.com/boostorg/hana/issues/162  

Is there an easy way to use tags to discriminate types but still retain the original functionality with functions that don't support the tags?  
  
Consider the overview example which I've modified with pseudocode demonstrating what I wish to do. Is there a way to do something like:  
  
``` c++  
#include <boost/hana.hpp>  
#include <boost/hana/ext/std/type_traits.hpp>  
#include <cassert>  
#include <string>  
using namespace boost::hana;  
using namespace boost::hana::literals;  
  
struct Fish{};  
struct Cat{};  
struct Dog{};  
  
tag<std::string,Fish> FishStr;  
tag<std::string,Cat> CatStr;  
tag<std::string,Dog> DogStr;  
  
int main() {  
  // Sequences capable of holding heterogeneous objects, and algorithms  
  // to manipulate them.  
  auto animals = make_tuple(FishStr{"Nemo"}, CatStr{"Garfield"}, DogStr{"Snoopy"});  
  auto names = transform(animals, [](auto a) {  
    return a;  
  });  
  assert(reverse(names) == make_tuple("Snoopy", "Garfield", "Nemo"));  
  auto fish = filter(animal_types, [](auto a) {  
    return traits::is_tagged(a,Fish());  
  });  
  static_assert(fish == make_tuple(type<FishStr>), "");  
}  
```  
  
I don't think this would compile, but is there something to that effect available in the repository?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-07-10 05:10:23 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-120228292  

Does the following do what you want?  
  
``` c++  
#include <boost/hana.hpp>  
#include <cassert>  
#include <string>  
using namespace boost::hana;  
using namespace std::literals;  
  
struct Fish { };  
struct Cat { };  
struct Dog { };  
  
int main() {  
  auto animals = make_tuple(make_pair(type<Fish>, "Nemo"s),  
                            make_pair(type<Cat>, "Garfield"s),  
                            make_pair(type<Dog>, "Snoopy"s));  
  auto fish = filter(animals, [](auto t) {  
    return first(t) == type<Fish>;  
  });  
  assert(fish == make_tuple(make_pair(type<Fish>, "Nemo"s)));  
}  
```  
  
You can then use `keys` or `transform` to get only the relevant part of the pair (the type or the string), as needed.

---

## Comment 2

> Username: ahundt  
> Created at: 2015-07-10 06:44:40 UTC  
> Updated at: 2015-07-10 06:49:46 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-120251887  

Cool, that definitely solves the problem when I can define the types myself! I love how concise hana can be.  
  
That lead me to another possibility. User defined types may be where pair syntax breaks down, because a struct with several data members containing say, vectors of torques positions and velocities couldn't go through a simple make_pair.  
  
How would you approach incorporating user defined data like `PetStore` below into an architecture that can be used like your example above in addition to the existing types we already defined above?  
  
``` c++  
struct PetStore {  
std::vector<std::string> fish;  
std::vector<std::string> cats;  
std::vector<std::string> dogs;  
}  
  
struct PetsAtHome {  
  std::string dog;  
  char[55]   cat;  
  std::string gerbil;  
}  
  
typedef std::vector<PetsAtHome> PetsInNeigborhood;  
```  
  
To provide a clearer picture here are the goals I'm imagining:  
- a small metaprogram to serialize/deserialize type discriminated data  
- separation of data structures and algorithms, perhaps via a small metaprogram to unpack the data from user defined types to feed data into an algorithm, such as how boost.geometry supports them.   
- algorithms that perform calculations based on not just the type but also the unit of the data, like in boost.units  
- conversion of data from one user defined format to another based on the tag data supplied with the function call, such as copying data from PetStore to each of PetsAtHome and PetsInNeigborhood to the extent possible.  
  
I know that's outside the bounds of my initial question but one thing leads to another... I'm still learning the right way to think about these things so I really appreciate your feedback.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-07-10 22:00:47 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-120538728  

Can you please clarify what you mean by _type discriminated data_? Do you mean an object (of a C++ type `T`), which is associated to another type `D`. Then, you're interested in manipulating objects of type `T`, while still keeping the information contained in type `D` associated to those objects?  
  
For example, `T` could be `int` and `D` could be _meter per seconds_. Then, you're interested in manipulating `int`s tagged with some unit information to do dimensional analysis. Is this sort of what you're trying to accomplish, or is it something else entirely? I'm trying to understand your problem better so I can help you.

---

## Comment 4

> Username: Manu343726  
> Created at: 2015-07-11 11:39:13 UTC  
> Updated at: 2015-07-11 11:41:59 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-120606809  

This reminds me to the meta type system I'm working on with Concepts Lite. I have boxed types which carry type information of the C++ type they wrap, the concept is similar to Rust traits. The you can easily discriminate types with a concept constraint:  
  
``` cpp  
template<is_a<Integer> i>  
struct foo { ... };  
```  
  
Where `is_a` is a binary concept matching a Typeclass with a C++ (boxed) type:  
  
``` cpp  
template<typename Tag, typename T>  
constexpr bool is_a()  
{  
    return std::is_same<Tag, typename T::typeclass>::value;  
}  
```  
  
GCC-clite currently does not support concept constraints on lambdas, so this currently does not fit very well with Hana. I'm looking forward for more CL support, for my experience it makes meta (type manipulation in general) clean and concise. Imagine Hana functions with the same kind of constraints:  
  
``` cpp  
auto no_pointers = remove_if(animal_types, [](Type a) {  
  return traits::is_pointer(a);  
});  
```  
  
Picked from the tutorial. By using a concept constraint on the lambda parameter, when we use that filter with non-type values it fails at the top level, not by accessing to `::type` inside `traits::is_pointer`.  
  
_I still have the idea of translating Hana concepts to Concepts Lite on my roadmap, but I have no enough time yet. Also I want to release my experiments on the algebraic meta type system first, which is my sandbox for testing CL._

---

## Comment 5

> Username: ldionne  
> Created at: 2015-07-11 11:43:06 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-120606891  

I'll happily jump on the Concepts-lite wagon when it's here, and I think that it will indeed make metaprogramming much cleaner.

---

## Comment 6

> Username: ahundt  
> Created at: 2015-07-12 15:40:31 UTC  
> Updated at: 2015-07-12 15:45:42 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-120731294  

I'll get really specific to eliminate confusion, but I think what I want to accomplish applies more broadly.  
  
I'm working on robotics and there are different models of arms with completely different implementations and control capabilities. The reason I made the PetStore and so on is because I have to deal with different structures. Sometimes an arm has 5-7 joints, and different arms have different sensors. So I'll have info like position, velocity, torque, which move pretty easily between different implementations but may be stored in different ways and have different underlying reasons. For example there is the "commanded" position (angle theta) and torque that I send to the arm. Then there is a "Monitor" aka measured position and torque which is what actually happened, and this can differ from what I told it to do. However, there are also APIs that work in cartesian rather than joint space, where I tell it to go to [x,y,z,roll,pitch,yaw]. Or [x,y,z,qw,qx,qy,qz] where q is a quaternion, a 4 element way to represent rotations more accurately.  
  
Then, the APIs can implement different subsets in totally different ways like these:  
  
``` c++  
  
struct jointMonitor{  
   double position;  
   double  torque;  
   double external_torque;  
}  
  
struct jointCommand {  
   double position;  
   double torque;  
   double external_torque;  
}  
  
enum JointState {  
  ok,  
err1,  
err2  
};  
  
enum JointMode {  
   Position,  
   Velocity,  
   Cartesian  
}  
  
struct arm1 {  
   std::vector<JointMonitor> monitorJoints;  
   std::vector<JointCommand> commandJoints;  
  JointMode jointmode;  
  JointState jointstate;  
}  
  
struct arm2 {  
  std::vector<double> receivedPositions;  
  std::vector<double> commandPositions;  
  std::vector<double> commandCartesian; // x,y,z,r,p,y  
  std::string errorCode;   
  bool enableBrakes;  
}  
  
```  
  
In addition to the arm APIs, there are also simulator APIs too, which leads to an M*N explosion of APIs to implement. As you can see, these simplified "designs" have some fundamental similarities and some fundamental differences.   
  
You can also sort of see how I was trying to generalize this with animal types, homes, and pet stores. The same sort of problem applies to almost any two implementations of similar technologies so I think this is more broadly applicable than my specific example, as boost.geometry, boost.qvm, and boost.asio are examples of.   
  
I'm not trying to solve all the problems in the world, but what I am interested in is having a straightforward way to adapt input data in a way that a metaprogram can understand and continue to work with the pieces that are available even if not all 100% of components exist on each API. Then for most typical cases I'd like to be able to write a simple adapter from the native data implementation so a metaAPI can read and write data and turn my O(M*N) implementations problem into an O(M+N) problem.  
  
My picture of how to implement that is to tag the elements of the native data storage with the both the dimensional analysis component and the state component, then call a simple metafunction like copy(), which then can go through each matching data component and copy the data from one format to another, or can take two completely different formats, and do some math on each with one implementation.   
  
I think boost.geometry achieves this for a subset of the problem, and I feel like hana provides fantastic tools that could be used to that end while making things much simpler and more powerful while allowing variation in the data that's available. That's why I asked my question because I'd like to learn from your experience with a very small example that I could use as the foundation. Does this make sense and seem both possible and practical? Thanks a million for your time and input!

---

## Comment 7

> Username: ldionne  
> Created at: 2015-07-14 20:49:15 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-121380630  

Ok, I have re-read this a couple of times to get a better picture. I'll now dump my understanding of the problem; let me know if I'm off the track.  
  
I think you need a "master API", one which contains a superset of the functionality provided by all the arms you might want to control. Then, you only use the arms through that API, and since it is at least as general as every other arm API, you are guaranteed to be able to control any arm properly. Do we agree on this?  
  
Then, you need to somehow map these specific arm implementations to this "most powerful API" in one way or another, so that you can use these arms through that (single) API. Overall, given `N` arm implementations and `M` different "master" APIs (master arm API, master simulator API, etc...), you need to implement `N x M` APIs in total: `M` APIs for each of the `N` different implementations. This is what you're trying to avoid and turn into an `N + M` problem, right?  
  
I'm not sure how Hana fits into the picture yet, but let's not give up.

---

## Comment 8

> Username: ahundt  
> Created at: 2015-07-15 05:36:43 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-121495226  

Correct! That's basically my target.  
  
The API centric way you are thinking of it is interesting and may be better than my initial idea. I believe I was originally thinking in a more data centric way which is where I was picturing hana being useful to define maps and/or sequences of the data contained in formats like `arm1` and `arm2` at compile time, then use hana to generate the implementations that move/access data between formats, such as from one container design to another at runtime.  
  
A "master" API which I hook "slave" APIs into does sound interesting. To elaborate on my situation, there are a number of different APIs that could be moved between. One example is a physical robot's driver APIs where I read and write data to update the state, usually by inserting and reading containers from a class over a network interface [sample driver tests are linked in this question of mine](https://stackoverflow.com/questions/31194443/boost-asio-async-udp-socket-has-high-latency). My initial attempts at a "master API" (no hana, or even much metaprogramming) are in [grl](https://github.com/ahundt/grl) which attempts to wrap [V-REP](coppeliarobotics.org) at the moment. Example tools that could be wrapped include [KDL](http://www.orocos.org/kdl) and [cisst](https://github.com/jhu-cisst/cisst/wiki) for example. Each tool, simulator, and robot API contains different containers for linear algebra data and series of joint data.  APIs for existing algorithm implementations could be wrapped in the master API you describe which can access the underlying specialized API implementations.  
  
Of course as described above this could expand infinitely so it is sensible to make a surgical selection of critical parts, like accessing data in a generic way and automatically converting among several varying data layouts, particularly transforms, series of joint data (with unit/dimensional analysis component), and codes for modes/states/errors.    
  
Perhaps a hana metaprogram acting on tags and a type sequence paired with the existing runtime data types could be used to define accessors. The compile time sequence can be walked over to generate code that copies or directly accesses common parts of data with one implementation for a variety of runtime data types like `arm1` and `arm2`. Ultimately this defines the "master api" to be used at runtime.  
  
Does that make any sense?

---

## Comment 9

> Username: ldionne  
> Created at: 2015-07-17 19:12:39 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-122378031  

Helping you properly requires a quite good understanding of your problem domain. Unfortunately, I lack both such knowledge and the time to acquire it at the moment, so it is hard for me to sketch a solution for you.  
  
What I definitely can do, if you provide a skeleton of what you need (even pseudocode would be a good start), is to give you comments about how you might simplify it or write it in idiomatic Hana. If you formulate your needs in terms of C++ and pseudo-Hana, I can then probably help you without having to know the problem domain.

---

## Comment 10

> Username: ahundt  
> Created at: 2015-07-17 23:59:08 UTC  
> Updated at: 2015-07-17 23:59:46 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-122457865  

Ok, I think I've reduced it to something managable. Can you explain how to do something like the [boost.geometry convert function](https://github.com/boostorg/geometry/blob/master/include/boost/geometry/algorithms/convert.hpp) with hana?  
  
Here is the "primary" function definition, but you need to look at the link above to really capture everything, though I'm only looking for the gist of how it would be done:  
  
``` C++  
/*!  
\brief Converts one geometry to another geometry  
\details The convert algorithm converts one geometry, e.g. a BOX, to another  
geometry, e.g. a RING. This only works if it is possible and applicable.  
If the point-order is different, or the closure is different between two  
geometry types, it will be converted correctly by explicitly reversing the  
points or closing or opening the polygon rings.  
\ingroup convert  
\tparam Geometry1 \tparam_geometry  
\tparam Geometry2 \tparam_geometry  
\param geometry1 \param_geometry (source)  
\param geometry2 \param_geometry (target)  
\qbk{[include reference/algorithms/convert.qbk]}  
 */  
template <typename Geometry1, typename Geometry2>  
inline void convert(Geometry1 const& geometry1, Geometry2& geometry2)  
{  
    resolve_variant::convert<Geometry1, Geometry2>::apply(geometry1, geometry2);  
}  
```  
  
I think hana can cut down on the complexity of the above boost.geometry code, or at least write it in a different way. Correct?  
  
Also here is a [reasonable example from my library](https://github.com/ahundt/grl/blob/master/include/grl/vrep/Eigen.hpp):  
  
``` C++  
namespace vrepquat {  
    enum vrep_quat {  
       x,  
       y,  
       z,  
       w  
    };  
}  
  
template<typename InputIterator> // I think this isn't actually being used as an input iterator, ignore that...  
Eigen::Quaterniond vrepToEigenQuaternion(InputIterator vrepQuat){  
  
  //                  0123  
  //  vrep is ordered xyzw,  
  // eigen is ordered wxyz  
  
    Eigen::Quaterniond quat(vrepQuat[vrepquat::w],vrepQuat[vrepquat::x],vrepQuat[vrepquat::y],vrepQuat[vrepquat::z]);  
    return quat;  
}  
  
template<typename Q>  
std::array<float,4> EigenToVrepQuaternion(const Q& q){  
  std::array<float,4> qa;  
  
  //                  0123  
  //  vrep is ordered xyzw,  
  // eigen is ordered wxyz  
  qa[vrepquat::x] = q.x();  
  qa[vrepquat::y] = q.y();  
  qa[vrepquat::z] = q.z();  
  qa[vrepquat::w] = q.w();  
  
  return qa;  
}  
```  
  
There are a few problems with my code I'd ideally eliminate:  
- It is nowhere near as general as the boost version  
- I have to write out the conversion function for every pair of libraries I implement in each direction  
- the names will different in every case (in my function)  
- I have to manually deal with the element ordering difference between the functions  
- I'd like to be able to choose between ignoring and failing on element mismatches  
  
Ideally I'd have something like the following pseudocode:  
  
``` C++  
// compile time generator pseudocode  
// this is where the bulk of hana code goes  
//  
// todo:  
// maybe have a second loop for iterating over depth, like with   
// tuple<tuple<datatocopytype>> datatoCopy;  
// datatocopytype whereToPutIt;  
// copy(dataToCopy,whereToPutIt);  
  
t2& apply_copy (t1,t2, strategy)  
{  
  upt1 = unpack_data_type(t1);  
  upt2 = unpack_data_type(t2);  
  are_compatible_types(upt1,upt2);  
  for(element_type:upt1)  
  {  
   if(units_mustmatch(strategy)) BOOST_VERIFY(units(upt1)==units(upt2));  
    if(argument_mismatch_allowed(strategy))  
    {  
      get(upt2,element_type) = get(upt1,element_type);  
    }  
     else  
    {  
      compiler_error_mismatched_not_allowed();  
    }  
  }  
}  
  
// runtime   
// side note: Perhaps the "copy" calls should be named convert?  
template<type1,type2,strategy=default_strategy_type()>  
type2 copy(type1 t1,type2 t2,strategy =default_strategy())  
{  
return  apply_copy(t1,t2);   
}  
  
struct ThirdPartyQuaternion {  
double w;  
double x;  
double y;  
double z;  
};  
  
struct ThirdPartyPoint{  
double x;  
double y;  
double z;  
};  
  
struct ThirdPartyTransform{  
ThirdPartyQuaternion q;  
ThirdPartyPoint           p;  
};  
  
typedef std::tuple<ThirdPartyPoint,ThirdPartyQuaternions> MyTransform;  
  
main()  
{  
auto data = make_tuple(eigen::quaterniond(1,0,0,0),eigen::point3d(1,1,1));  
// tag locally identifies the underlying type as a quaternion, but the class stays the same otherwise  
// this differs slightly from the global registration boost.geometry provides.  
  
tag<std::array<double,4>,quaternion_tag> quaternion;  
ThirdPartyTransform theirTransform;  
MyTransform myTransform;  
  
copy(data,quaternion); // data copies ok  
copy(data,quaternion,disable_mismatch()); // fails because quaternion can't handle all elements  
copy(data,theirTransform); // ok  
copy(theirTransform,myTransform); // ok  
copy(myTransform,data); // ok  
copy(quaternion,myTransform,disable_mismatch()); // fails because all elements don't match  
copy(quaternion,myTransform); // ok  
}  
```  
  
Going one step further, I'd like to have the copy calls  check the "unit", so in the case of points they could be distance vectors, or velocity vectors, and you shouldn't be able to add the two.  
  
A bonus would be an additional optional statically defined reference frame. For example if you are standing next to me with a coffee can nearby, the vector from me to a coffee can and from you to a coffee can are both valid. However, they shouldn't mix because they are measured in different reference frames, so if I tried to walk to the coffee can using your vector I'd end up somewhere completely wrong.

---

## Comment 11

> Username: ldionne  
> Created at: 2015-07-19 09:37:53 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-122643644  

From my understanding, you could start from something like:  
  
``` c++  
struct default_strategy {  
    template <typename T1, typename T2>  
    void operator()(T1& t1, T2& t2) const {  
        BOOST_VERIFY(units(t1) == units(t2)); // up to you to implement `units`  
        t1 = t2;  
        // Here, you could also decide to trigger a compile-time error if  
        // there's a type mismatch between T1 and T2; you decide.  
    }  
};  
  
template <typename T1, typename T2, typename Strategy = default_strategy>  
T2& copy(T1& t1, T2& t2, Strategy strategy = default_strategy{}) {  
    auto n = hana::length(t1);  
    BOOST_HANA_CONSTANT_ASSERT(hana::length(t2) == n);  
    BOOST_HANA_CONSTANT_ASSERT(  
        are_compatible(t1, t2) // up to you to implement `are_compatible`  
    );  
  
    n.times.with_index([&](auto i) {  
        strategy(hana::at(t2, i), hana::at(t1, i));  
    });  
  
    return t2;  
}  
```  
  
Whatever types `T1` and `T2` you use in there will have to be models of some Hana concepts so that you can call `at`, `length` and stuff like that on them. Another possibility is to convert everything to Hana tuples with `hana::to<hana::Tuple>`, but that will make a copy of your data and then you will lose the ability to return a meaningful reference to `T2` from `copy`.   
  
Otherwise, for `std::tuple` and `std::array`, it should work fine if you include the proper extension headers (`<boost/hana/ext/std/{tuple,array}.hpp>`). For other custom types, you'll have to make them a model of (at least) `Foldable` and `Iterable` to get `length` and `at`. You can look up the documentation of these concepts to know which functions must be implemented. Note that this part of Hana is about to change quite a bit in a non-backward compatible way (for much better performance), so you might not want to base any _real world_ work on this just yet.  
  
Also note that I'll be gone until around next Sunday and with little internet connectivity, so I might not be able to answer in a timely fashion until then.

---

## Comment 12

> Username: ahundt  
> Created at: 2015-07-19 16:13:44 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-122676716  

Ok, enjoy your trip! I'll hold of on actually implementing something "real" until after the changes, thanks for the heads up.   
  
One small thing, would that snippet you posted be able to handle the ordering differences? I thought a map might be necessary for cases where data is ordered xyzw vs wxyz, because the types/units wouldn't match up in the implementation of default_strategy. Maybe I'll first have to build a map of all the elements at least for one side, then go through the list of other elements and do lookups to see if they are present, and copy the data if they are.

---

## Comment 13

> Username: ldionne  
> Created at: 2015-07-24 19:22:50 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-124655611  

Is this what you're talking about?  
  
``` c++  
struct default_strategy {  
    template <typename T1, typename T2>  
    void operator()(T1& t1, T2& t2) const {  
        BOOST_VERIFY(is_permutation(units(t1), units(t2)));  
        // ...  
    }  
};  
```  
  
If so, then you could use a set to hold the units, and ordering differences would be ignored automatically when comparing sets for equality.

---

## Comment 14

> Username: ahundt  
> Created at: 2015-07-27 14:59:32 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-125237159  

wow, seriously amazing! If when the new updates come out this compiles quickly too this will be a truly impressive library. So much complicated compile time and template code can be reduced to a single line now.

---

## Comment 15

> Username: ahundt  
> Created at: 2015-07-27 23:52:58 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-125381070  

While browsing the web I came across [an article](https://blog.quasardb.net/5-awesome-c-libraries-we-use/) which brought me to [BOOST_HANA_ADAPT_STRUCT](http://ldionne.com/hana/structboost_1_1hana_1_1Struct.html).  That looks like another piece of the puzzle I'm looking to solve. While this adaptation only takes the type and accessor of the structs, perhaps a version is possible where I can supply `(classname,(descrimination_tag,type,membername))`. Then I could say:  
  
``` C++  
BOOST_HANA_TAGGED_ADAPT_STRUCT(ThirdPartyPoint,(x_tag,double,x),(y_tag,double,y),(z_tag,double,z))  
```  
  
then if there is another way this is done:  
  
``` C++  
typedef std::array<float,3> point; // zyx  
  
BOOST_HANA_TAGGED_ADAPT_SEQUENCE(ThirdPartyPoint,(x_pos_tag,double,[2]),(y_pos_tag,double,[1]),(z_pos_tag,double,[0]))  
```  
  
then the function:  
  
``` C++  
dostuff(){  
point p{3,2,1};  
ThirdPartyPoint p2;  
copy(p1,p2);  
std::cout << get(p,x_pos_tag()); // ok  
std::cout << get(p,x_velocity_tag()); // compile time error  
}  
```  
  
the x,y,z tags could somehow be defined using their dimension like in boost.geometry and a unit.

---

## Comment 16

> Username: ldionne  
> Created at: 2015-08-01 08:24:03 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-126884846  

I am struggling to understand how Hana would help you in this case, but I might be missing what you're trying to achieve in the end. It seems to me that something like the following would work:  
  
``` c++  
#include <array>  
#include <iostream>  
  
  
struct x_position { };  
struct y_position { };  
struct z_position { };  
  
struct x_velocity { };  
  
template <typename Point, typename Tag>  
auto get(Point const&, Tag) = delete;  
  
template <typename To, typename Point>  
To convert_to(Point const& p) {  
    return To{get(p, x_position{}),  
              get(p, y_position{}),  
              get(p, z_position{})};  
}  
  
  
  
struct ThirdPartyPoint { double x, y, z; };  
double get(ThirdPartyPoint const& p, x_position) { return p.x; }  
double get(ThirdPartyPoint const& p, y_position) { return p.y; }  
double get(ThirdPartyPoint const& p, z_position) { return p.z; }  
  
  
using Point = std::array<double, 3>;  
double get(Point const& p, x_position) { return p[0]; }  
double get(Point const& p, y_position) { return p[1]; }  
double get(Point const& p, z_position) { return p[2]; }  
  
  
int main() {  
    Point p1{{3, 2, 1}};  
    ThirdPartyPoint p2 = convert_to<ThirdPartyPoint>(p1);  
  
    std::cout << get(p2, x_position{}); // ok  
    std::cout << get(p2, x_velocity{}); // compile time error  
}  
```  
  
Now, were Hana might actually help you is in the definition of a more complex `copy` function as sketched above, or in any place where you need to programatically manipulate heterogeneous sequences.

---

## Comment 17

> Username: ahundt  
> Created at: 2015-08-03 16:50:03 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-127329257  

Programmatically manipulating heterogeneous sequences is what I'm aiming to do. These get functions and convert functions are the basic API upon which I hope to build a library. If I can set up a way to have the dimension of the point be a meta compile time value, then I can write algorithms that work for 2 and 3 dimensional points, then objects containing heterogeneous sequences of points plus other data types.

---

## Comment 18

> Username: ldionne  
> Created at: 2015-08-18 20:01:46 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-132333528  

To have the dimension something on which you can operate programmatically, you could replace the `*_position` structs by  
  
``` c++  
template <int n>  
struct position { };  
```  
  
Then, you might want to say `position<0>` for `x_position`, `position<1>` for `y_position` and so on.  
  
At any rate, I'm not sure where this issue is going, so I'm going to close it. I'm happy to provide help for application-specific questions, but this seems like a bit too open-ended for me to give useful advice.

---

## Comment 19

> Username: ahundt  
> Created at: 2015-08-22 04:14:35 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-133633486  

ok, thanks for the help

---

## Comment 20

> Username: ahundt  
> Created at: 2015-08-24 03:19:39 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-134002917  

I found an [interesting and relevant article](https://rodgert.github.io/page2/) that uses mpl to deal with type driven data. It doesn't explain hooking existing types into a system like they describe, but it does explain how to define new ones.

---

## Comment 21

> Username: ldionne  
> Created at: 2015-08-26 12:09:52 UTC  
> Url: https://github.com/boostorg/hana/issues/162#issuecomment-134972940  

Thanks for the heads up! With Hana, you can use `BOOST_HANA_DEFINE_STRUCT` and `BOOST_HANA_ADAPT_STRUCT` to achieve something similar.
