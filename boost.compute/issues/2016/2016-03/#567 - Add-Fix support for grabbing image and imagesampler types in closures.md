# #567 - Add/Fix support for grabbing image and imagesampler types in closures [Closed]

> Username: koosha94  
> Created at: 2016-03-08 01:38:27 UTC  
> Updated at: 2017-04-22 14:04:50 UTC  
> Closed at: 2017-04-22 14:04:50 UTC  
> Url: https://github.com/boostorg/compute/issues/567  

This code should work, however it fails to build due to type_trait issues.  
  
```  
BOOST_COMPUTE_CLOSURE(Node, calculate_scores, (Node node), (imageSampler,posXImage,negXImage,posYImage,negYImage,posZImage,negZImage),{  
        if(timestamp>0){  
            answer.score = 32767; //max short value  
        }  
        else{  
            unsigned char side = node.index&0x7;  
            unsigned short y = (node.index>>3)&0x7ff;  
            unsigned short x = (node.index>>14)&0x7ff;  
            unsigned char lodVal = ((node.index>>25)&0x7f);  
            unsigned char lod = maxLOD - lodVal;  
            x = x<<lod +((1<<lod)>>1);//find middle X  
            y = y<<lod +((1<<lod)>>1);//find middle Y  
            int4 imValue;  
            Node answer = node;  
            switch (case side){  
                    0:  
                    imValue = read_imagei(posXImage,imageSampler,(int2)(x,y));  
                    break;  
                    1:  
                    imValue = read_imagei(negXImage,imageSampler,(int2)(x,y));  
                    break;  
                    2:  
                    imValue = read_imagei(posYImage,imageSampler,(int2)(x,y));  
                    break;  
                    3:  
                    imValue = read_imagei(negYImage,imageSampler,(int2)(x,y));  
                    break;  
                    4:  
                    imValue = read_imagei(posZImage,imageSampler,(int2)(x,y));  
                    break;  
                    5:  
                    imValue = read_imagei(negZImage,imageSampler,(int2)(x,y));  
                    break;  
            }  
            answer.score = (unsigned short)(imValue.r)+(unsigned short)(lod)*64;  
        }  
    });  
```  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2016-03-08 03:53:33 UTC  
> Url: https://github.com/boostorg/compute/issues/567#issuecomment-193592706  

MR #569 adds support for `type_name()` with `opengl_texture` and `opengl_renderbuffer`.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-04-22 14:04:50 UTC  
> Url: https://github.com/boostorg/compute/issues/567#issuecomment-296375423  

Closing. Please reopen if there's still a problem.
