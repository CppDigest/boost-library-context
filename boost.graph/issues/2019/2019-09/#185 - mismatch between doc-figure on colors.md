# #185 - [boykov-kolmogorov] mismatch between doc/figure on colors [Open]

> Username: eolivi  
> Created at: 2019-09-06 12:06:00 UTC  
> Updated at: 2019-09-06 12:06:00 UTC  
> Url: https://github.com/boostorg/graph/issues/185  

In the lib documentation,  
https://www.boost.org/doc/libs/1_70_0/libs/graph/doc/boykov_kolmogorov_max_flow.html  
the figure shows a white  Source node and a black Sink node, whether it is previously written that:  
> the source and the sink are colored (source==black, sink==white)  
  
This is confusing. Either change the picture or the code/doc.  
  
IMO the source should be white and not black (i.e change the code and doc), but it would be too complicated to keep backward compatibility...
