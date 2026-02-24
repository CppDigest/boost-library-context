# #863 - How can I return a custom struct from a BOOST_COMPUTE_FUNCTION [Open]

> Username: phillvancejr  
> Created at: 2021-05-18 16:42:28 UTC  
> Updated at: 2021-05-18 16:42:28 UTC  
> Url: https://github.com/boostorg/compute/issues/863  

Lets say I have  a pixel struct and want to loop through two vectors of pixels add the components of the two pixels together and then put them in another vector, how can I return a new struct from transform?  
  
```c++  
struct pixel {  
  int r, g, b, a;  
};  
  
BOOST_COMPUTE_ADAPT_STRUCT(pixel, pixel, (r,g,b,a))  
  
//main  
  
  
vector<pixel> image1( size, pixel{0,0,0,1});  
vector<pixel> image2( size, pixel{1,0,0,1});  
vector<pixel> result( size, pixel{0,0,0,1});  
  
// setup up device, context, queue and device vectors ...  
  
BOOST_COMPUTE_FUNCTION(pixel, add_images, (pixel a, pixel b),  
{  
  pixel out;  
  out.r = a.r + b.r;  
  // etc....  
    
  return out;  
});  
  
  
compute::transform(  
  device_image1.begin(),  
  device_image1.end(),  
  device_image2.begin(),  
  device_result.begin(),  
  add_images,  
  queue  
);  
  
// get results...  
```  
  
How do I allocate memory for the result pixel inside the Compute Function?
