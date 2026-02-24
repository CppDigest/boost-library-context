# #39 - build error with fibonacci_heap in Boost 1.71.0 and C++11 [Open]

> Username: For-Chance  
> Created at: 2024-04-12 12:19:24 UTC  
> Updated at: 2024-04-12 14:27:57 UTC  
> Url: https://github.com/boostorg/heap/issues/39  

It can build successfully with Boost 1.83.0 and C++14. But it build wrong with Boost 1.71.0 and C++11. How can I  do if I don't update the version?  
``` c++  
template <typename State, typename Action, typename Cost, typename Environment, typename StateHasher = std::hash<State> >  
class AStar{  
public:  
/* other code */  
private:  
    struct Node {  
        Node(const State& state, Cost fScore, Cost gScore)  
            : state(state), fScore(fScore), gScore(gScore) {}  
    
        bool operator<(const Node& other) const {  
            // Sort order  
            // 1. lowest fScore  
            // 2. highest gScore  
    
            // Our heap is a maximum heap, so we invert the comperator function here  
            if (fScore != other.fScore) {  
                return fScore > other.fScore;  
            }  
            else {  
                return gScore < other.gScore;  
            }  
        }  
    
        friend std::ostream& operator<<(std::ostream& os, const Node& node) {  
            os << "state: " << node.state << " fScore: " << node.fScore  
                << " gScore: " << node.gScore;  
            return os;  
        }  
    
        State state;  
    
        Cost fScore;  
        Cost gScore;  
    
        typename boost::heap::fibonacci_heap<Node>::handle_type handle;		// error with Boost 1.71.0 and C++11  
        };  
}  
```  
output  
```  
2>d:\hcx\projects\autopiping\include\MAPF/a_star.hpp(213): error C2039: 'handle_type' : is not a member of 'boost::heap::fibonacci_heap<libMultiRobotPlanning::AStar<State,Action,int,Environment,std::hash<State>>::Node,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_>'  
2>          D:\include\temp\vcpkg\installed\x64-windows\include\boost/heap/detail/stable_heap.hpp(227) : see reference to class template instantiation 'libMultiRobotPlanning::AStar<State,Action,int,Environment,std::hash<State>>::Node' being compiled  
2>          D:\include\temp\vcpkg\installed\x64-windows\include\boost/heap/fibonacci_heap.hpp(60) : see reference to class template instantiation 'boost::heap::detail::heap_base<T,std::less<T>,true,unsigned __int64,false>' being compiled  
2>          with  
2>          [  
2>              T=libMultiRobotPlanning::AStar<State,Action,int,Environment,std::hash<State>>::Node  
2>          ]  
2>          D:\include\temp\vcpkg\installed\x64-windows\include\boost/heap/fibonacci_heap.hpp(137) : see reference to class template instantiation 'boost::heap::detail::make_fibonacci_heap_base<T,boost::parameter::aux::empty_arg_list>' being compiled  
2>          with  
2>          [  
2>              T=libMultiRobotPlanning::AStar<State,Action,int,Environment,std::hash<State>>::Node  
2>          ]  
2>          d:\hcx\projects\autopiping\include\MAPF/a_star.hpp(223) : see reference to class template instantiation 'boost::heap::fibonacci_heap<libMultiRobotPlanning::AStar<State,Action,int,Environment,std::hash<State>>::Node,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_,boost::parameter::void_>' being compiled  
2>          D:\hcx\projects\AutoPiping\src\PathFinding.cpp(509) : see reference to class template instantiation 'libMultiRobotPlanning::AStar<State,Action,int,Environment,std::hash<State>>' being compiled  
```
