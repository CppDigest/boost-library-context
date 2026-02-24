# #72 - [voronoi] The diagram has an infinite segment that origin point inside in closed polygon. [Open]

> Username: SiTiGi  
> Created at: 2022-02-18 15:41:01 UTC  
> Updated at: 2022-05-06 07:22:38 UTC  
> Url: https://github.com/boostorg/polygon/issues/72  

Boost version 1.75.0.  
Created a voronoi diagram for a given closed polygon. The diagram has an infinite segment(ray) that origin point inside in polygon. As far as I understand, such cases should not be for closed polygons and second point of this segment must on the perimeter. Also in this case, two segments of the diagram intersect.  
Source polygon:  
[Perimeter.txt](https://github.com/boostorg/polygon/files/8098118/Perimeter.txt)  
Diagram (Blue segments are polygon. Black segments are primary. Green segments are secondary.):  
![image](https://user-images.githubusercontent.com/38733446/154708220-927f8f78-d561-4708-aec9-92e207efd631.png)  
Infinity segment (Blue segments are polygon. Infinite segment(ray) is red. Black segments are primary segments inside polygon and not touching perimeter.):  
![image](https://user-images.githubusercontent.com/38733446/154708678-0c946d60-6ba8-4959-81b6-eed6c6be2149.png)  
Segments intersect (Blue segments are polygon. Black segments are primary. Green segments are secondary.):  
![image](https://user-images.githubusercontent.com/38733446/154708474-a5999ccc-c185-4783-bae8-0f8d227f1924.png)

---

## Comment 1

> Username: bubnikv  
> Created at: 2022-05-06 07:22:37 UTC  
> Url: https://github.com/boostorg/polygon/issues/72#issuecomment-1119330815  

Will it fix if you rotate the polygon slightly?  
I have a feeling that there are some issues with strictly vertical lines.
