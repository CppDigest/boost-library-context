# #513 - Blocking effect in CLAHE algorithm(non-overlapping interpolated version) [Closed]

> Username: codejaeger  
> Created at: 2020-08-17 19:52:21 UTC  
> Updated at: 2020-08-24 20:20:33 UTC  
> Closed at: 2020-08-18 11:26:09 UTC  
> Url: https://github.com/boostorg/gil/issues/513  

### Description  
I am working on adding a new algorithm which is a version of adaptive histogram equalization. It divides the image into tiles which share their boundaries with the neighboring tiles. Then histogram equalization is performed locally on each tile and to finally prevent blocking artifacts a bi-linear interpolation using the mappings from neighboring tiles is applied.  
  
For example,  
if my input image is  
0 0 1 1  
0 0 1 1  
2 2 3 3  
2 2 3 3  
  
I would perform histogram equalization locally on each of the 0's, 1's blocks etc. separately.  
Then for the lower right pixel in 0's block (1, 1), I calculate the final value using bi linear interpolation on the mappings obtained (by performing local histogram equalization) for each of the 0, 1, 2 and 3 blocks.  
   
### Problem:  
  
Bi linear interpolation works perfectly for the area it is applied on, but it does not remove the blocking effect completely.  
For example when applied on a standard image of size 512x512, using tiles of 100x100, notice how the top row blocks of 50x100 differs from the vertically below blocks (100x100) due (difference arising due to interpolation using different mappings) :  
![barb-ahe](https://user-images.githubusercontent.com/32168969/90435445-a5653b00-e0ec-11ea-9ad0-9d4773ba3218.png)  
  
The block with top left corner at (50x50) and bottom right corner at (150, 150) is obtained by applying bi linear interpolation on the mappings obtained from the 100x100 tiles with top left corners at (0, 0), (0, 100), (100, 0), (100, 100).  
The output(right) image clearly shows how bi linear interpolation works correctly for each individual section of image it is applied to, but does not remove the blocking effect on a global scale.  
The reason could be probably because the original image has almost uniform distribution of pixels on a global scale but not locally.  
  
<!-- A clear and concise description of what the problem is. Ex. I'm always frustrated when [...] -->  
  
<!-- C++ program or snippet that shows preferred solution -->  
  
### Alternatives  
  
I have read up on an alternative version of the algorithm that uses sliding windows to update the pixel at each point, but it is computationally much more expensive.  
  
### Additional context  
  
I am referencing the algorithm from :  
1. http://cas.xav.free.fr/Graphics%20Gems%204%20-%20Paul%20S.%20Heckbert.pdf Pg. 474  
&  
2. http://www.cs.unc.edu/Research/Image/MIDAG/pubs/papers/Adaptive%20Histogram%20Equalization%20and%20Its%20Variations.pdf  
  
I have re-read the text in the reference to verify if I might have committed a mistake on my side.  
Any help on this is appreciated.

---

## Comment 1

> Username: codejaeger  
> Created at: 2020-08-18 11:26:09 UTC  
> Url: https://github.com/boostorg/gil/issues/513#issuecomment-675422336  

I figured out that the issue was not with the algorithm but with the way I was applying the bi linear interpolation on the pixels.  
For pixels that were lying in the same row/column as the sample pixels (around which the mappings were constructed), one of the weights would go to zero and leaving the other non zero, this would cause the denominator to scale up disproportionately.   
The solution was to calculate the sample weights as fractions beforehand and then apply the masking and get the interpolated pixel value.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-08-18 17:48:54 UTC  
> Url: https://github.com/boostorg/gil/issues/513#issuecomment-675622481  

Great finding @codejaeger !
