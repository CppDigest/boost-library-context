# #591 - Guidance for GSOC 2021 [Closed]

> Username: ameyachawla99  
> Created at: 2021-03-29 17:34:58 UTC  
> Updated at: 2021-04-03 17:11:41 UTC  
> Closed at: 2021-04-03 17:11:41 UTC  
> Url: https://github.com/boostorg/math/issues/591  

Respected sir/madam  
  
Hi , I am Ameya Chawla currently pursuing my B.Tech in Computer Science and Engineering .  
I read about your organization in GSOC 2021 and looking forward to work with your organization .  
I want to work on project Implement a feasible realization of FFT .  
  
Please Guide me how should i start working  
  
Thank You

---

## Comment 1

> Username: ckormanyos  
> Created at: 2021-03-29 18:47:53 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-809623870  

> work on project Implement a feasible realization of FFT  
 > Please Guide me how should i start working  
Hello Ameya,  
Thank you for your interest in our projectand for your query. Since you found theproject, please consult its description atthat place.  
As for moving forward, you need to formallyapply for this project using the established GSoCapplication procedure.  
As written in other posts, you have to familiarizeyourself with the official GSoC guides for students.  
Google offers extensive documentation on that.  
If you have even more detailed questions, pleasefeel free to contact us via this thread.  
Kind regards, Christopher Kormanyos  
   
  
    On Monday, March 29, 2021, 7:35:18 PM GMT+2, ameyachawla99 ***@***.***> wrote:    
   
   
  
  
Respected sir/madam  
  
Hi , I am Ameya Chawla currently pursuing my B.Tech in Computer Science and Engineering .  
I read about your organization in GSOC 2021 and looking forward to work with your organization .  
I want to work on project Implement a feasible realization of FFT .  
  
Please Guide me how should i start working  
  
Thank You  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Comment 2

> Username: ameyachawla99  
> Created at: 2021-03-30 08:10:27 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810012966  

In Competency Test do we have to implement FFT or we can implement any transformation like Laplace transformation ?  
  
Do we have to submit competency results in proposal or on github?  
  
Thank You

---

## Comment 3

> Username: ckormanyos  
> Created at: 2021-03-30 09:45:48 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810079662  

> In Competency Test do we have to implement FFT or we can implement any transformation like Laplace transformation ?  
  
Please feel free to implement anything in that general area. This is a chance for you to show that you can handle programming numerical transformations. The selection of the exact transformation you choose is not the essential part of this competency expression.  
  
> Do we have to submit competency results in proposal or on github?  
  
In the past, successful candidates have posted the results of the competency test as a small repo on GitHub, GitLab or similar. This repo can be readily linked to within the larger scope of the application.

---

## Comment 4

> Username: ameyachawla99  
> Created at: 2021-03-30 13:43:31 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810259568  

I have implemented FFT .   
Please can you review my code and guide me for any changes to be made.  
  
https://github.com/ameyachawla99/math/tree/develop/FFT

---

## Comment 5

> Username: ckormanyos  
> Created at: 2021-03-30 14:56:33 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810330503  

> I have implemented FFT.  
> Please can you review my code and guide me for any changes to be made.  
  
You might consider the variable `y` [at this line](https://github.com/ameyachawla99/math/blob/fc335f60919fde7121a9bd5fb0260224faabfcca/FFT/FastFourierTransform.cpp#L35). I believe the memory pointed to by `y` is allocated with `operator new`. In a very quick glance, I was not able to find the deletion of this complex-valued array.  
  
You might consider another form of handling the result within the context of the recursive call or the FFT.

---

## Comment 6

> Username: ameyachawla99  
> Created at: 2021-03-30 15:40:36 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810368330  

I will update the code .

---

## Comment 7

> Username: ameyachawla99  
> Created at: 2021-03-30 16:36:18 UTC  
> Updated at: 2021-03-30 16:36:28 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810408559  

> You might consider another form of handling the result within the context of the recursive call or the FFT.  
  
  
Should i change the implementation and use  different method to store results other than dynamic arrays?

---

## Comment 8

> Username: ameyachawla99  
> Created at: 2021-03-30 18:08:35 UTC  
> Url: https://github.com/boostorg/math/issues/591#issuecomment-810468900  

>>I was not able to find the deletion of this complex-valued array.  
  
I tried freeing the space using free method but "double free error was raised"  , i think they were freed when they were out of scope .
