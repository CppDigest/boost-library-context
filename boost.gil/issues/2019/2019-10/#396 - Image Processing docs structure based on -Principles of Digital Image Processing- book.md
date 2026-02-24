# #396 - Image Processing docs structure based on "Principles of Digital Image Processing" book [Open]

> Username: mloskot  
> Created at: 2019-10-13 18:00:19 UTC  
> Updated at: 2020-04-17 17:27:27 UTC  
> Url: https://github.com/boostorg/gil/issues/396  

### Overview  
This is a task to organize **documentation** of our the **Image Processing** features in GIL following on structure of the topics as proposed in the 3-volume series of the **Principles of Digital Image Processing** book ([1](https://www.springer.com/gp/book/9781848001909), [2](https://www.springer.com/gp/book/9781848001947), [3](https://www.springer.com/gp/book/9781848829183)).  
  
Simply, just following the table of contents of those books would help to come up with structure that is clean, maintainable, _future-proof_ and friendly to readers, those familiar with image processing concepts and GIL as well as newbies.  
  
### Task  
  
Below is proposal of the the "Image Processing table of contents.  
Please, _feel free to comment and discuss_ it in the comments below or following the [thread on the boost-gil mailing list](https://lists.boost.org/boost-gil/2019/10/0306.php).  
  
### ToC: Image Processing  
  
- Histograms and Statistics  
   - Grayscale images  
   - Color images  
- Basic Point Operations - _algebraic and logical operations to modify pixel values without changing size, geometry or structure of image_                                                                                                                                                                                      
   - Intensity (contrast , brightness, inverting, simple global thresholding)  
   - Histogram Equalization  
   - Gamma Correction  
   - Color Space Transformations  
- Filters (grayscale and color images)  
   - Linear  
   - Non-linear  
   - Morphological  
- Classification and Segmentation - _methods for labeling, binarizing and finding_  
   - Thresholding  
      - Simple Global Thresholding  
      - Automatic Thresholding  
         - Global Adaptive Thresholding  
         - Local Adaptive Thresholding  
   - Labeling Regions  
- Detectors - _methods for object detection_  
   - Edges   
   - Curves  
   - Corners  
   - Contours  
- Geometric Operations - _operations altering image geometry_  
- Image Transforms - _operators altering geometry and nature of the image information as well as geometry of the image itself_  
   - Distance Transform  
   - Fourier Transform (DFT, FFT)  
   - Cosine Transform (DCT)  
   - Haar Transform  
   - Hough Transform  
  
### Changes History  
  
Changes of the proposal above following comments and discussions from you!  
  
1. Initial proposal  
...

---

## Comment 1

> Username: ArbitCode  
> Created at: 2020-01-21 21:15:00 UTC  
> Url: https://github.com/boostorg/gil/issues/396#issuecomment-576884936  

can I try to look into the issue to get overview of algorithms that are used while image processing?

---

## Comment 2

> Username: mloskot  
> Created at: 2020-01-21 21:39:42 UTC  
> Updated at: 2020-01-21 21:40:05 UTC  
> Url: https://github.com/boostorg/gil/issues/396#issuecomment-576894908  

You're welcome to give it a go, but be warned its not a quick bit of work.  
Instead of diving deep, you can help @lpranam with collecting the image processing algorithms to the wish list here  https://github.com/boostorg/gil/wiki/Image-Processing-Algorithms

---

## Comment 3

> Username: codejaeger  
> Created at: 2020-03-08 06:56:52 UTC  
> Url: https://github.com/boostorg/gil/issues/396#issuecomment-596173559  

@mloskot Not that I am rushing from one issue to another, but I just remembered [this](http://boost.2283326.n4.nabble.com/Boost-GIL-GSoC-2020-FFT-implementation-td4711253.html) mailing thread (on **FFT** in Boost.Gil) which I had started as an initial proposal to my GSoC project idea .This had recieved some nice inputs from various people. Although , I was not well equipped at that time to continue researching on this topic , I feel we could add this idea to the [wiki](https://github.com/boostorg/gil/wiki/Image-Processing-Algorithms) of GIL so that if anyone(in future) wants to take a look at this, they would not have to dig it out from the other mails.   
Tagging @lpranam also.  
Thanks.

---

## Comment 4

> Username: mloskot  
> Created at: 2020-03-08 22:39:38 UTC  
> Url: https://github.com/boostorg/gil/issues/396#issuecomment-596261294  

@codejaeger https://github.com/boostorg/gil/wiki/Image-Processing-Algorithms is a general list of the IP features and algorithms, it is not just related to GSoC, so feel free to add a row to the table with link the FFT thread there.  
  
For GSoC specifically, although students are welcome to find and suggest their project ideas, it is a [common way in finding the right project](https://google.github.io/gsocguides/student/finding-the-right-project), [finding a topic that both they and the mentor is interested in](https://github.com/boostorg/wiki/wiki/Google-Summer-of-Code:-Overview) may be difficult, especially if there is a shortage of mentors. I personally would prefer to focus on completing the work on the basics first as continuation of the previous year.

---

## Comment 5

> Username: codejaeger  
> Created at: 2020-03-09 06:20:33 UTC  
> Updated at: 2020-03-09 06:32:46 UTC  
> Url: https://github.com/boostorg/gil/issues/396#issuecomment-596350334  

@mloskot I have updated the [page](https://github.com/boostorg/gil/wiki/Image-Processing-Algorithms) accordingly  
  
Regarding   
  
> I personally would prefer to focus on completing the work on the basics first as continuation of the previous year  
  
I have thought of a proposal not entirely related to   
  
> completing the work on the basics first as continuation of the previous year.  
  
But it is mentioned in the current GSoC ideas page . Is that okay in the sense will I find a mentor w.r.t those projects? Thanks.

---

## Comment 6

> Username: mloskot  
> Created at: 2020-03-09 07:24:34 UTC  
> Url: https://github.com/boostorg/gil/issues/396#issuecomment-596368370  

@codejaeger There are mentors available, two mentors, so yes you (as well as other students submitting their proposals) have chance to find mentor.
