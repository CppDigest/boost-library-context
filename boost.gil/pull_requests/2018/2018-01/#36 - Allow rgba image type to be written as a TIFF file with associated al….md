# #36 Allow rgba image type to be written as a TIFF file with associated al… [Closed]

> Username: gallafent  
> Created at: 2018-01-16 16:35:09 UTC  
> Updated at: 2018-01-16 16:40:41 UTC  
> Closed at: 2018-01-16 16:38:03 UTC  
> Url: https://github.com/boostorg/gil/pull/36  

…pha channel.  
  
Colour channels are premultiplied by the alpha value, and those premultiplied channels are written to the TIFF file with the alpha value as an extra component.   
  
Experimentation indicated that saving TIFFs in this way resulted in files which were most likely to be loaded and correctly interpreted by other software (Adobe Photoshop, Apple Preview, …), at least according to the requirements I had at the time (producing “masked” images containing image elements extracted from the original image). The alternative approach (not premultiplying the colours, and saving the alpha as “unassociated”) produces images which are not displayed as intended by much software (which is to be expected … the “unassociated” nature of such an alpha channel means that the program loading the file does not know what the purpose of the alpha component is, and so should not do anything regarding displaying it without the intervention of the user).  
  
As per https://www.awaresystems.be/imaging/tiff/tifftags/extrasamples.html , this appears to be the most correct way to represent an image with partially transparent pixels “automatically”. It might be that one would want to add a parameter to the TIFF writer to choose whether to use associated or unassociated alpha to save an rgba image, for more flexibility in future.  
  
“The difference between associated alpha and unassociated alpha is not just a matter of taste or a matter of maths.  
  
“Associated alpha is generally interpreted as true transparancy information. Indeed, the original color values are lost in the case of complete transparency, and rounded in the case of partial transparency. Also, associated alpha is only logically possible as the single extra channel.  
  
“Unassociated alpha channels, on the other hand, can be used to encode a number of independent masks, for example. The original color data is preserved without rounding. Any number of unassociated alpha channels can accompany an image.”

---
