# #858 - how to reduce "array of custom user structures" with custom function ? [Open]

> Username: RayTraceX  
> Created at: 2020-12-20 22:41:14 UTC  
> Updated at: 2020-12-20 22:42:40 UTC  
> Url: https://github.com/boostorg/compute/issues/858  

for example needs something that,  
  
`  
__declspec(align(16))  
struct Box_aligned {  
float3 v0;  
float3 v1;  
}  
  
`boost::compute::vector<Box_aligned> boxes(10, context);`  
// and do something with values of array "boxes"  
  
Box_aligned bounds;  
  
boost::compute::reduce(boxes.begin(), boxes.end(), &bounds, BoxUnion(),  queue);  
`  
  
where "some BinaryFunction" is somthing lika that,  
  
  
struct BoxUnion {  
	Box_aligned operator()(const Box_aligned b1, const Box_aligned b2) const {  
		Box_aligned bounds;  
		bounds.v0 = fmin(b1.v0, b2.v0);  
		bounds.v1 = fmax(b1.v1, b2.v1);  
		return bounds;  
	}  
};  
  
  
how to reduce such array of "Box_aligned" values ?
