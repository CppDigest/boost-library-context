# #334 Add Image Processing section to Doxygen-based docs [Merged]

> Username: mloskot  
> Created at: 2019-07-12 14:14:06 UTC  
> Updated at: 2020-02-22 13:04:26 UTC  
> Merged at: 2019-07-12 14:56:52 UTC  
> Closed at: 2019-07-12 14:56:52 UTC  
> Url: https://github.com/boostorg/gil/pull/334  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Enable API reference geneartion for the GSoC 2019 features.  
  
-----  
  
@simmplecoder & @miralshah365 FYI, this should fix the problem you were reporting on Gitter about docs for your code.  
The critical part is `\defgroup ImageProcessing` added in https://github.com/boostorg/gil/compare/develop...mloskot:ml/doc-add-image-processing?expand=1#diff-99612ff6bcbef4bb2ccde36f09ea3884R192  
  
Then, enable grouping with `ImageProcessing` for your headers, http://www.doxygen.nl/manual/grouping.html, using `\ingroup` or `\addtogroup`.

---
