# #31 - TGA [Closed]

> Username: ohhmm  
> Created at: 2017-12-30 10:33:20 UTC  
> Updated at: 2018-03-07 18:44:02 UTC  
> Closed at: 2018-03-07 18:44:02 UTC  
> Url: https://github.com/boostorg/gil/issues/31  

I found this code and think it maybe useful. In case you need TGA its right here:  
```  
//  
// Book:      OpenGL(R) ES 2.0 Programming Guide  
// Authors:   Aaftab Munshi, Dan Ginsburg, Dave Shreiner  
// ISBN-10:   0321502795  
// ISBN-13:   9780321502797  
// Publisher: Addison-Wesley Professional  
// URLs:      http://safari.informit.com/9780321563835  
//            http://www.opengles-book.com  
//  
  
// esUtil_TGA.c  
//  
//    This file contains the Win32 implementation of a TGA image loader  
  
#define WIN32_LEAN_AND_MEAN  
#include <windows.h>  
#include <stdio.h>  
#include <stdlib.h>  
  
///  
//  Macros  
//  
#define INVERTED_BIT            (1 << 5)  
  
///  
//  Types  
//  
#pragma pack(push,x1)                            // Byte alignment (8-bit)  
#pragma pack(1)  
  
typedef struct  
{  
   unsigned char  IdSize,  
                  MapType,  
                  ImageType;  
   unsigned short PaletteStart,  
                  PaletteSize;  
   unsigned char  PaletteEntryDepth;  
   unsigned short X,  
                  Y,  
                  Width,  
                  Height;  
   unsigned char  ColorDepth,  
                  Descriptor;  
           
} TGA_HEADER;  
  
#pragma pack(pop,x1)  
  
////////////////////////////////////////////////////////////////////////////////////  
//  
//  Private Functions  
//  
  
////////////////////////////////////////////////////////////////////////////////////  
//  
//  Public Functions  
//  
//  
  
  
///  
//  WinTGALoad()  
//  
int WinTGALoad( const char *fileName, char **buffer, int *width, int *height )  
{  
   FILE        *fp;  
   TGA_HEADER   Header;  
  
   if ( fopen_s ( &fp, fileName, "rb" ) != 0 )  
   {  
      return FALSE;  
   }  
  
   if ( fp == NULL )  
   {  
      return FALSE;  
   }  
  
   fread ( &Header, sizeof(TGA_HEADER), 1, fp );  
  
   *width = Header.Width;  
   *height = Header.Height;  
     
   if ( Header.ColorDepth == 24 )  
   {  
      RGBTRIPLE *Buffer24;  
  
      Buffer24= (RGBTRIPLE*)malloc(sizeof(RGBTRIPLE) * (*width) * (*height));  
  
      if(Buffer24)  
      {  
         int i=0;  
         int x,  
             y;  
  
         fread(Buffer24, sizeof(RGBTRIPLE), (*width) * (*height), fp);  
  
         *buffer= (LPSTR) malloc(3 * (*width) * (*height));  
  
         for ( y = 0; y < *height; y++ )  
            for( x = 0; x < *width; x++ )  
            {  
               int Index= y * (*width) + x;  
  
               if(!(Header.Descriptor & INVERTED_BIT))  
                  Index= ((*height) - 1 - y) * (*width) + x;  
  
               (*buffer)[(i * 3)]=      Buffer24[Index].rgbtRed;  
               (*buffer)[(i * 3) + 1]=  Buffer24[Index].rgbtGreen;  
               (*buffer)[(i * 3) + 2]=  Buffer24[Index].rgbtBlue;  
          
               i++;  
            }  
           
         fclose(fp);  
         free(Buffer24);  
         return(TRUE);  
      }		  
   }  
  
   return(FALSE);  
}  
  
```

---

## Comment 1

> Username: chhenning  
> Created at: 2017-12-30 15:14:49 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-354551198  

Is TGA the same as the Targa file format? We do have Targa:  
  
https://github.com/boostorg/gil/blob/develop/include/boost/gil/extension/io/formats/targa/

---

## Comment 2

> Username: ohhmm  
> Created at: 2018-01-01 14:56:01 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-354657362  

I posted this code in case if you want load TGA w/o using additional third party libraries.

---

## Comment 3

> Username: chhenning  
> Created at: 2018-01-01 18:36:16 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-354668410  

I might be wrong but I don't think we are using a 3rd party lib. Can you point me to the code?

---

## Comment 4

> Username: ohhmm  
> Created at: 2018-01-02 08:17:47 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-354718668  

You are right, thank you. By the way, tga load error, saved by Gimp 2.8.22  
[g.tga.zip](https://github.com/boostorg/gil/files/1597042/g.tga.zip)

---

## Comment 5

> Username: chhenning  
> Created at: 2018-01-02 18:39:45 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-354842859  

Works for me:  
  
```  
rgb8_image_t img;  
read_image("D:\\g.tga", img, targa_tag());  
  
write_view("D:\\gradient_gil.bmp", view(img), bmp_tag());  
```

---

## Comment 6

> Username: ohhmm  
> Created at: 2018-01-03 08:55:38 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-354962556  

Got it! If read image through file stream then it throws about wrong header if file was not found.

---

## Comment 7

> Username: chhenning  
> Created at: 2018-01-03 13:52:18 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-355016445  

Could you send me a simple code example?

---

## Comment 8

> Username: ohhmm  
> Created at: 2018-01-04 19:07:41 UTC  
> Updated at: 2018-01-04 19:08:24 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-355370262  

```  
     ifstream in( "none.tga", ios::binary );   
     rgb8_image_t img;   
     read_image( in, img, targa_tag() );   
```

---

## Comment 9

> Username: chhenning  
> Created at: 2018-01-04 19:57:34 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-355382838  

This is works for me:  
```  
#include <iostream>  
#include <fstream>  
#include <numeric>  
#include <vector>  
  
#include <boost/gil/extension/io/bmp_all.hpp>  
#include <boost/gil/extension/io/targa_all.hpp>  
  
using namespace boost::gil;  
  
int main()  
{  
    std::ifstream in("D:\\g.tga", std::ios::binary);  
    rgb8_image_t img;  
    read_image(in, img, targa_tag());  
  
    write_view("D:\\gradient_gil.bmp", view(img), bmp_tag());  
  
    return 0;  
}  
```

---

## Comment 10

> Username: ohhmm  
> Created at: 2018-01-04 21:54:44 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-355409974  

if file was not found

---

## Comment 11

> Username: ohhmm  
> Created at: 2018-01-05 09:48:31 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-355514113  

If read image through file stream then it throws about wrong header if file was not found.  
But nothing wrong with the header.  
The stream was empty. Eof check needed.

---

## Comment 12

> Username: mloskot  
> Created at: 2018-03-07 18:29:05 UTC  
> Url: https://github.com/boostorg/gil/issues/31#issuecomment-371236220  

Can this be closed as per @chhenning -s change https://github.com/boostorg/gil/commit/a26f61b55d140dc731ccf1c27e90d9b58a3f8b11?
