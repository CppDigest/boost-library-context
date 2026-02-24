# #857 - how to fill array of custom user structures ? [Open]

> Username: RayTraceX  
> Created at: 2020-12-20 22:14:14 UTC  
> Updated at: 2020-12-20 22:23:57 UTC  
> Url: https://github.com/boostorg/compute/issues/857  

for example needs something that,  
  
`  
struct Box {  
		float3 v0;  
		float3 v1;  
  
		Box() {  
			v0 = float3(M_INF, M_INF, M_INF);  
			v1 = float3(-M_INF, -M_INF, -M_INF);  
		}  
};  
__declspec(align(16))  
struct Node {  
		int index;  
		int offset;  
		Box box;  
  
		Node() {  
			index = -1;  
		}  
};  
  
`boost::compute::vector<Node> nodes(10, context);`  
  
Node node_default;  
  
boost::compute::fill(nodes.begin(), nodes.end(),   
node_default, // compile error with that  
queue);  
`  
  
how to fill array "nodes" with value "node_default" ?
