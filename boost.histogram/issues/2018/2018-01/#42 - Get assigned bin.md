# #42 - Get assigned bin [Closed]

> Username: manuel-vh  
> Created at: 2018-01-16 14:03:25 UTC  
> Updated at: 2019-01-19 13:58:19 UTC  
> Closed at: 2019-01-19 13:58:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/42  

Hi, ¿is there any way to know assigned bin number for each data value(s) added?  
Example: in a 2D histogram of points (x, y) each point has a charge and I need a way to perform additional calcs with charge value for each bin.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-01-16 15:20:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-357993638  

Hi, short answer is no, currently you cannot get the assigned bin number for each data value added. But if you have a good case why this should be possible, I am happy to add this functionality.  
  
Currently, the call to ```histogram::fill(...)``` returns void, but I could change it so that it returns a type that represents the bin index. Could you please explain a bit more, what exactly you want to achieve? I am worried that this is going to leak internal implementation details to the user and/or reduce performance, so I want to make sure that you really need this functionality and there is no other efficient way to get what you want.

---

## Comment 2

> Username: manuel-vh  
> Created at: 2018-01-16 21:33:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-358113854  

Hi Hans, thanks a lot for your fast answer.  
  
I've been using your histogram code to create regular histograms and I've  
found it a very interesting library that matches my needs and I love his  
functionality ( and frankly speaking I'm trying to keep aside from ROOT  
code for the non-simulation tasks).  
  
My job is related to atmospheric muon detectors development, so I need to  
deal with 1D, 2D and 3D  histograms , not only accumulating a number of  
points per bin, also I need to calculate some additional properties for  
each bin.  
For example, i'd like to use  histogram lib to voxelize 3D space and  
calculate  additional properties for each voxel ( histogram bin in this  
case )  where each 3D point have more variables assigned ( scatter angle,  
trajectory relative displacement,  statistic quality variable, etc...)  
Just now I work with 500K-800K point dataset  and probably I'll need to  
process millions in a near future, so is very convenient to avoid  
processing the point dataset several times, otherwise in some situations, I  
will be forced to write my own  'ad hoc' code to build histogram while  
performing  additional operations at the same time as I move over the  
entire dataset.  
  
BTW, I think you've done a very good job with histogram lib and I wish it  
to be part of boost project soon.  
  
My best regards  
Manuel  
  
  
On Tue, Jan 16, 2018 at 4:20 PM, Hans Dembinski <notifications@github.com>  
wrote:  
  
> Hi, short answer is no, currently you cannot get the assigned bin number  
> for each data value added. But if you have a good case why this should be  
> possible, I am happy to add this functionality.  
>  
> Currently, the call to histogram::fill(...) returns void, but I could  
> change it so that it returns a type that represents the bin index. Could  
> you please explain a bit more, what exactly you want to achieve? I am  
> worried that this is going to leak internal implementation details to the  
> user and/or reduce performance, so I want to make sure that you really need  
> this functionality and there is no other efficient way to get what you want.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/HDembinski/histogram/issues/42#issuecomment-357993638>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AXJXj8PxoP6EEJZrUu8vyo1Ie4qSMH1Uks5tLL4sgaJpZM4Rf0hK>  
> .  
>

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-01-19 16:57:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-359026325  

Hi Manuel,  
  
thank you for your praise, I am glad that you find the library useful! :)  
  
Could you be a bit more specific what exactly you would like to compute in addition to counting things? I am asking, because there is an alternative that I am considering. You can already plug your own storage policy into the histogram, so technically you could implement an alternative storage that computes arbitrary extra values from each input and assign it to a bin.  
  
Strictly speaking, you probably don't need to write a whole storage policy, you just need to adapt the type that serves as a counter. You could replace that by a class which does not only counting but also other things with the input.  
  
If you give a bit more details on what you want to compute, I could work on an example for this kind of usage and put that in the user guide.

---

## Comment 4

> Username: HDembinski  
> Created at: 2018-03-06 22:00:53 UTC  
> Updated at: 2018-03-06 22:02:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-370944320  

This issue made me realize that I tightly coupled the interface of the counter type with the histogram interface without a good reason. This is going to be changed to allow more freedom in changing the counter type. I am still not sure how to let `fill(...)` return the bin index in an efficient way.  
  
1) Return a flat index. Easy to go back to the same counter in the storage, hard to get the corresponding axis bins. I assume that @manuel-vh wanted to access the axis bins, too, so that would not work for him.  
  
2) Return a full index, which allows to access the corresponding axes bins. This would require larger changes to the internal logic, which currently only computes the axis indices on the fly.

---

## Comment 5

> Username: manuel-vh  
> Created at: 2018-03-07 13:51:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-371143935  

Not sure about the best solution as I have not a detailed knowledge of class inner working. Regarding my currents needs, after calling fill(...) method for each sample, getting assigned bin index will allow me to perform additional operations beyond histogram features. As an example among others related to my current tasks:  I have a set of data points in a 3-dimensional space plus a scatter angle assigned to each point  P(x, y, z, theta).  Histogram allows me to fast group points in spatial bins but I also need to perform a volumetric scatter angle density calculation per each bin and build a map (bin_number, scatter_angle_density). How to achive this?. Returning bin index from fill(...) method will add some stack push/pop cost. Perhaps another way is to add a new method to retrieve last  assigned bin after a fill(...) call, let's say we call it getLastBin() or something alike. This will keep current interface backward compatibility. Users who don't need that feature have not more worries.

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-03-08 10:54:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-371453539  

Thank you for providing additional information, that is helpful for me, and I think I have something in mind that would fill your need. I think what you want is an N-dimensional profile. In ROOT, you have TProfile. It accepts x,y pairs, and compute the average of y in bins of x. If I understand you correctly (please confirm), you want something like a TProfile3, where you have x,y,z,theta pairs, and you want to bin things along x,y,z, and compute the average of theta in each bin of x,y,z.  
  
I am working in the development branch on enabling this kind of usage.

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-03-08 12:46:18 UTC  
> Updated at: 2018-03-08 12:55:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-371476972  

Ah wait, on second read, maybe you just want to add another dimension to the histogram, over the theta-axis, so to say? You want the density of scatter angles, not only their mean. So you would make a histogram from four axes, three for the spatial bins and the forth for the theta angle. You fill the histogram. This gives you counts, not a density along the theta axis, but you can compute the density after filling, like so  
```  
// h is a filled 4D histogram with axes x,y,z,theta  
using boost::histogram::literals; // enable _c suffix  
auto h_summed_over_theta = h.reduce_to(0_c, 1_c, 2_c);  
// iterate over values and compute theta density  
for (auto xbin : h.axis(0_c))  
  for (auto ybin : h.axis(1_c))  
     for (auto zbin : h.axis(2_c))  
{  
    auto m = h_summed_over_theta.value(xbin.first, ybin.first, zbin.first);  
    // this loops over the theta density axis  
    for (auto tbin : h.axis(3_c)) {  
       auto n = h.value(xbin.first, ybin.first, zbin.first, tbin.first);  
       auto theta_density = n / m / (tbin.second.upper() - tbin.second.lower());  
       // do something with theta_density  
    }  
}

---

## Comment 8

> Username: manuel-vh  
> Created at: 2018-03-09 22:38:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-371965505  

Thanks a lot for your invaluable help. At first sight,  it seems this can solve at least one of my current needs. I need to test with real data and check results.

---

## Comment 9

> Username: HDembinski  
> Created at: 2018-03-10 19:33:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-372058713  

i really see your point that fill should return an index. i need to do some benchmarks...

---

## Comment 10

> Username: manuel-vh  
> Created at: 2018-03-19 01:21:31 UTC  
> Updated at: 2018-03-19 01:32:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374075620  

Hi, I can successfully get points frequency for each bin, but I'm unable to accumulate theta value for each x, y, z bin. To me  x, y, z dimension --> voxel  
**Theta range from 0 to 200 mrad**  
  
  
`    //create a static 4d-histogram  from point dataset P(x,y,z, theta)  
    auto h4d = bh::make_static_histogram(  
            bh::axis::regular<>(voxelsX, -halfRangeX, halfRangeX, "x"),  
            bh::axis::regular<>(voxelsY, -halfRangeY, halfRangeY, "y"),  
            bh::axis::regular<>(voxelsZ, -halfRangeZ, halfRangeZ, "z"),  
            bh::axis::regular<>(thetaRange, 0, thetaRange, "theta")  
            );  
`      
.....  
`  // loop to fill the histogram`  
` h4d.fill(vx, vy, vz, theta);`    
    .....  
`  size_t voxelIndex{}; `   
`  auto h_summed_over_theta = h4d.reduce_to(0_c, 1_c, 2_c); `   
`    // iterate over values and compute theta density `   
`  for (auto xbin : h4d.axis(0_c)) `   
`     //std::cout << xbin.second.lower() << ", " << xbin.second.upper() << "\n"; `   
`     for (auto ybin : h4d.axis(1_c)) `   
`        for (auto zbin : h4d.axis(2_c))`   
`        { // Voxel bin   
`             voxelIndex++;  // My volxel number `   
            auto m = h_summed_over_theta.value(xbin.first, ybin.first, zbin.first);  
            std::cout << "voxel: " <<  voxelIndex << ", frecuency: " << m << "\n"; // Ok  
              
            // My intention here : accumulate theta values for current volxelIndex   
            for (auto thetabin : h4d.axis(3_c)) {  
  
                auto n = h4d.value(xbin.first, ybin.first, zbin.first, thetabin.first); // always returns 0  
                auto theta_density = n / m / (thetabin.second.upper() - thetabin.second.lower());  
  
                // thetabin.second.upper() - thetabin.second.lower()  always returns 200  
  
            }  
    }  
`

---

## Comment 11

> Username: HDembinski  
> Created at: 2018-03-19 21:25:08 UTC  
> Updated at: 2018-03-19 21:26:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374383969  

At first glance, all looks ok, except that `auto n = ...` is always zero. Could you check the under-/overflow bins in your theta axis? I think your theta values are outside of the axis range that you specified. If they are, then they end up either in the underflow or the overflow bin, depending on whether the values are too low or too high. In your last loop try this:  
```  
auto underflow = h4d.value(xbin.first, ybin.first, zbin.first, -1);  
auto overflow = h4d.value(xbin.first, ybin.first, zbin.first, h4d.axis(3_c).size());  
// print underflow and overflow  
```  
If one of these is quite large, then you know that the values are outside the range that you specified for the axis. You define your theta-axis as  
```  
bh::axis::regular<>(thetaRange, 0, thetaRange, "theta")  
```  
which is ok, if your theta values are in degrees. But maybe your thetas are negative?  
I think it is odd that `thetabin.second.upper() - thetabin.second.lower()` always returns 200. It should always return the same number for a `axis::regular`, but according to your axis definition it should return `1`, not `200`. What's the actual value of `thetaRange`?

---

## Comment 12

> Username: manuel-vh  
> Created at: 2018-03-20 00:06:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374427412  

Thanks for your help. I've made some additional tests while trying to understand histogram class. Let me explain better:   
theta angle comes from scalar product of two vectors and is expressed in milliradians (mrad). No negative values in data set.  Mostly of them in range (0 mrad, 200 mrad] which is my interval of interest for this variable. Most of overflow theta values are 0 or small numbers (1...5).  
`thetabin.second.upper() - thetabin.second.lower() == 1` because theta axis has 200 bins , width == 1 mrad.  
So for each 3D bin ( call it voxel ) I successfully can count number of points ( wich is not my main goal ):  
`auto m = h_summed_over_theta.value(xbin.first, ybin.first, zbin.first);`  
Now , I want to accumulate theta angle for each voxel:  
`auto n = h4d.value(xbin.first, ybin.first, zbin.first, thetabin.first);`  
I was wrong. n == 0  in many cases but I get positive frecuency values too.  
  
Just now I'am trying to sum all theta values in this way:  
```  
double accTheta{};  
for (auto thetabin : h4d.axis(3_c)) {  
       auto n = h4d.value(xbin.first, ybin.first, zbin.first, thetabin.first);  
       accTheta += thetabin.second.upper() * n;  
}   
```  
```  
Some output for accTheta:  
accTheta = 200  
accTheta = 49  
accTheta = 67  
accTheta = 56  
accTheta = 114  
accTheta = 119  
accTheta = 316  
accTheta = 166  
accTheta = 125  
accTheta = 147  
accTheta = 479  
accTheta = 51  
```  
  
In case I get right accTheta value I think my problem is solved.

---

## Comment 13

> Username: HDembinski  
> Created at: 2018-03-20 07:27:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374500637  

Ok, good to see progress :) Is your goal to compute a mean over the theta angle? If so I would suggest to compute `0.5 * (thetabin.second.lower() + thetabin.second.upper())` to get the bin center instead of the upper edge. If you use the upper edge of the bin, your mean computed in this way will have a positive bias

---

## Comment 14

> Username: manuel-vh  
> Created at: 2018-03-20 14:07:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374610476  

Yes, you're right!. I used `accTheta += thetabin.second.upper() * n` as simple example of possible addtionals operations to perform over theta. One thing which captured my attention is  `auto h_summed_over_theta = h.reduce_to(0_c, 1_c, 2_c);` Could you provide me with some link to fully understand reduce_to() operation?

---

## Comment 15

> Username: HDembinski  
> Created at: 2018-03-20 20:18:09 UTC  
> Updated at: 2018-03-20 20:18:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374742126  

> Could you provide me with some link to fully understand reduce_to() operation?  
  
It is described in the user guide, see http://hdembinski.github.io/histogram/doc/html/histogram/guide.html#histogram.guide.cpp.reductions  
Let me know if that is not clear, I will try to improve the text.

---

## Comment 16

> Username: HDembinski  
> Created at: 2018-03-20 20:19:30 UTC  
> Updated at: 2018-03-20 20:21:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374742550  

`reduce_to(0_c, 1_c, 2_c)` is returning a histogram that was summed over the theta axis. So it is doing what you do by hand now. If you are familiar with ROOT, there is a similar method in the `TH3` histograms, called `Project3D`. The numbers list the axis that you want to keep, the others are summed over.

---

## Comment 17

> Username: HDembinski  
> Created at: 2018-03-20 20:24:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-374743988  

Sorry, btw, the documentation is for the current master, some things in the interface of the histogram have changed since the last release. I hope you can figure out the differences, sorry about that.

---

## Comment 18

> Username: manuel-vh  
> Created at: 2018-04-30 09:43:59 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-385353278  

Thanks for this interesting class. We are obtaining very good and fast results when applied to muon scattering tomography, voxelización, etc... histogram class is definitely a  valuable tool.

---

## Comment 19

> Username: HDembinski  
> Created at: 2018-05-02 07:56:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-385894901  

Thanks! People like you who try out new things even if it means to be a pioneer of sorts keep me motivated!  
  
I still have some ideas to implement before the library will be submitted for review in Boost and the point you raised originally, that fill should return an index is one of them.

---

## Comment 20

> Username: HDembinski  
> Created at: 2019-01-19 13:58:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/42#issuecomment-455782373  

A fill now returns an iterator to the current bin in the develop branch.
