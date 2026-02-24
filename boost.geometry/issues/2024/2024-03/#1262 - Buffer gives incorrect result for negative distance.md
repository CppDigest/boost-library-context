# #1262 - Buffer gives incorrect result for negative distance [Closed]

> Username: jdmarsh  
> Created at: 2024-03-26 10:00:24 UTC  
> Updated at: 2024-03-27 09:46:07 UTC  
> Closed at: 2024-03-27 09:46:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/1262  

I have a polygon where I want to buffer inwards using a negative distance. The result I get is incorrect.  
  
Polygon(red) and then the result of the buffer command "output" (green).  
![image](https://github.com/boostorg/geometry/assets/972442/6aa5c629-8ff0-4fe9-bc5c-4df73345af9c)  
  
The expected result of this buffer is for the output to be empty, instead the green triangle is huge.  
- At the top left corner, it's buffered inwards but not by the -1.8 units specified.  
- At the top right corner, the output goes outside the outer boundary of the polygon  
- The center should certainly not be filled in  
  
The code to generate this can be found here:  
https://godbolt.org/z/18sq55oTG

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-03-27 08:56:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1262#issuecomment-2022243864  

Fixed recently in acaed28f1  
I will create a unit test

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-03-27 09:07:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1262#issuecomment-2022263481  

Thanks for the report.  
For the record, the PR was #1248
