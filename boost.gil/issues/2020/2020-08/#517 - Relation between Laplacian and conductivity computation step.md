# #517 - Relation between Laplacian and conductivity computation step [Open]

> Username: simmplecoder  
> Created at: 2020-08-25 18:47:18 UTC  
> Updated at: 2020-08-25 19:47:32 UTC  
> Url: https://github.com/boostorg/gil/issues/517  

Current interpretation:  
>Every pixel in the source is transformed into 9 pixels, with surrounding pixels being difference in that direction and the pixel itself is replaced by zero. Then kernel is computed for each pixel using conductivity function, convolved with the new 9 pixels, and then convolved by Laplacian kernel. This is the only explanation I have come up with that does the same as the implementation.  
  
So the question is "Is this right interpreation?"  
  
The original wording of the algorithm can be found here:  
http://www.sci.utah.edu/~gerig/CS7960-S2010/materials/Perona-Malik/anisotropic_diffusion-Book.pdf
