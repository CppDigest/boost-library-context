# #68 Incorporate GSoC 2015 Eigensolver into Develop branch [Open]

> Username: thomasyang1207  
> Created at: 2019-04-21 03:24:08 UTC  
> Updated at: 2021-09-09 12:45:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/68  

After reviewing the code and tests produced from Rajaditya Mukherjee's GSoC 2015 project, I wish to work towards merging this with the main development branch.   
  
The current develop branch does not contain methods for producing matrix inverses. In other words, there is no solver without external dependencies on existing routines like LAPACK, etc. Having one will allow many advanced algorithms involving matrices and tensors to be implemented.  
  
I'm sure there is a reason why it wasn't merged, but at the moment, including it in the development branch would make it much more accessible to other developers

---

## Comment 1

> Username: bassoy  
> Created_at: 2019-04-21 19:15:21 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-485275549  

Hi Thomas,  
  
thanks for your support. It will be good to merge previous GSOC projects  
and code to uBLAS.  I would like to see more than four test cases. Can you  
add more tests with different element types and matrix dimensions? Could  
you also reference the algorithms the gsoc student has used? It will be  
also good to measure the runtime and compare it with the runtime in octave  
for instance before we merge the code.  
  
Best  
Cem  
  
Am So., 21. Apr. 2019 um 05:24 Uhr schrieb Thomas <notifications@github.com  
>:  
  
> After reviewing the code and tests produced from Rajaditya Mukherjee's  
> GSoC 2015 project, I wish to work towards merging this with the main  
> development branch.  
>  
> The current develop branch does not contain methods for producing matrix  
> inverses. In other words, there is no solver without external dependencies  
> on existing routines like LAPACK, etc. Having one will allow many advanced  
> algorithms involving matrices and tensors to be implemented.  
>  
> I'm sure there is a reason why it wasn't merged, but at the moment,  
> including it in the development branch would make it much more accessible  
> to other developers  
> ------------------------------  
> You can view, comment on, or merge this pull request online at:  
>  
>   https://github.com/boostorg/ublas/pull/68  
> Commit Summary  
>  
>    - Hessenberg  
>    - Francis QR Step  
>    - FRancis Double Step QR  
>    - Transform Accumulations  
>    - Eigen Solver Wrapper  
>    - Givens Rotation and Modified Schur Decomposition  
>    - Complex Eigen Values  
>    - eigenvalue new routine  
>    - Complex Eigenvector  
>    - Small Numerical Fix  
>    - Matrix Balancing  
>    - Doxygen Comments  
>    - SingleTest and sample  
>    - Test Cases  
>    - HTML Docs  
>    - Matlab's Magic Shift Function  
>    - Merge GSOC 2015 eigensolver into Develop branch  
>  
> File Changes  
>  
>    - *A* doc/eigensolver.html  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-0> (146)  
>    - *M* doc/index.html  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-1> (6)  
>    - *A* doc/samples/eigen_solver.cpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-2> (54)  
>    - *A* include/boost/numeric/ublas/eigen_solver.hpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-3> (395)  
>    - *A* include/boost/numeric/ublas/hessenberg.hpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-4> (111)  
>    - *A* include/boost/numeric/ublas/householder.hpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-5> (107)  
>    - *A* include/boost/numeric/ublas/matrix_balancing.hpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-6> (115)  
>    - *A* include/boost/numeric/ublas/schur_decomposition.hpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-7> (533)  
>    - *M* test/common/testhelper.hpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-8> (25)  
>    - *A* test/test_eigensolver.cpp  
>    <https://github.com/boostorg/ublas/pull/68/files#diff-9> (117)  
>  
> Patch Links:  
>  
>    - https://github.com/boostorg/ublas/pull/68.patch  
>    - https://github.com/boostorg/ublas/pull/68.diff  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/pull/68>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AA6PDJJHI57DTS3ZXKVVJPDPRPM5RANCNFSM4HHK3L5Q>  
> .  
>

---

## Comment 2

> Username: yimyom  
> Created_at: 2019-04-23 13:56:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-485813462  

Really, the only reason was lack of time. I've kind of lost contact with the student too. I think he finished his PhD and was busy with his new work life, which I understand (every morning :-D ).  
We can work together on adding comparison with R, and Octave with Cem for example.  
  
Should I merge the branch now or we wait until you've got more benchmarks?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-04-23 14:27:38 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-485826608  

I would recommend performance comparison with Eigen. I used Eigen in this PR:  
  
https://github.com/boostorg/math/blob/b14975fa7f8eb49aec89aa8f51fb1615afe126b5/example/daubechies_scaling_integer_grid.cpp  
  
But of course I would've liked to remove the external dependency.

---

## Comment 4

> Username: thomasyang1207  
> Created_at: 2019-04-23 16:53:43 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-485886531  

At the very least, should we include a reference (let's say to a paper in arxiv) for each of the algorithms?   
  
I also think more benchmarks are important since the code is almost 4 years old

---

## Comment 5

> Username: bassoy  
> Created_at: 2019-04-23 20:20:23 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-485959081  

@David: I would like to "wait" until we have a stable version. And we have  
to review the code before any kind of merging I think. Nick if you want to  
participate on the review you are more than welcome.  
  
@Nick: I fully agree. Stefan already started to write a performance  
measurement library for ublas. Maybe we could do something more generic?  
  
@Thomas: Could you please include more unit tests with a higher degree of  
test coverage? Also we need to compare the results with the ones from  
octave. I will try to help.  
  
@Me: I will try to prepare unit-tests with which we can octave from my  
previous work. I have also my own tensor performance measurement tool.  
Maybe we could use that?  
  
  
Am Di., 23. Apr. 2019 um 18:53 Uhr schrieb Thomas <notifications@github.com  
>:  
  
> At the very least, should we include a reference (let's say to a paper in  
> arxiv) for each of the algorithms?  
>  
> I also think more benchmarks are important since the code is almost 4  
> years old  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/pull/68#issuecomment-485886531>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA6PDJJR3OY4WI65TBD5EO3PR45JPANCNFSM4HHK3L5Q>  
> .  
>

---

## Comment 6

> Username: yimyom  
> Created_at: 2019-04-27 12:06:35 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-487280607  

agree, agree, agree and agree... :-D  
  
On Wed, Apr 24, 2019 at 5:50 AM Cem Bassoy <notifications@github.com> wrote:  
  
> @David: I would like to "wait" until we have a stable version. And we have  
> to review the code before any kind of merging I think. Nick if you want to  
> participate on the review you are more than welcome.  
>  
> @Nick: I fully agree. Stefan already started to write a performance  
> measurement library for ublas. Maybe we could do something more generic?  
>  
> @Thomas: Could you please include more unit tests with a higher degree of  
> test coverage? Also we need to compare the results with the ones from  
> octave. I will try to help.  
>  
> @Me: I will try to prepare unit-tests with which we can octave from my  
> previous work. I have also my own tensor performance measurement tool.  
> Maybe we could use that?  
>  
>  
> Am Di., 23. Apr. 2019 um 18:53 Uhr schrieb Thomas <  
> notifications@github.com  
> >:  
>  
> > At the very least, should we include a reference (let's say to a paper in  
> > arxiv) for each of the algorithms?  
> >  
> > I also think more benchmarks are important since the code is almost 4  
> > years old  
> >  
> > —  
> > You are receiving this because you commented.  
> > Reply to this email directly, view it on GitHub  
> > <https://github.com/boostorg/ublas/pull/68#issuecomment-485886531>, or  
> mute  
> > the thread  
> > <  
> https://github.com/notifications/unsubscribe-auth/AA6PDJJR3OY4WI65TBD5EO3PR45JPANCNFSM4HHK3L5Q  
> >  
> > .  
> >  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/pull/68#issuecomment-485959081>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AAQOK4GT7EUF3ROBVOVHJRLPR5VQRANCNFSM4HHK3L5Q>  
> .  
>

---

## Comment 7

> Username: thomasyang1207  
> Created_at: 2019-04-27 21:03:40 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-487319554  

I added 5 more 10x10 test matrices with numbers at different orders of magnitude. I also changed the test suite so that it now covers the double and float data types. For some reason, my changes aren't showing up when I click on my new commit - any reason why this is?   
  
Right now, I'm still a bit uncertain whether the eigensolver can handle complex matrices... AFAIK Eigen has a separate eigensolver specifically for complex and hermitian matrices.  
  
I just realized that the eigensolver test suite was never added to the jamfile, so it was never run during build. For the eigensolver test suite and future test suites, do we add it manually to the Jamfile? Until then, we cannot merge anything  
  
For the new test cases I added, I also computed the same result in Eigen. Inside benchmarks I could potentially just copy the "Eigen" directory found inside the Eigen package, and then make the comparison there with the ublas eigensolver.   
  
There are also some other minor formatting changes I could do regarding some of the function signatures - adding const specifiers, etc. Some of the comments that rajaditya left also give us some unfinished tasks

---

## Comment 8

> Username: bassoy  
> Created_at: 2019-04-28 10:28:23 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-487366263  

Am Sa., 27. Apr. 2019 um 23:03 Uhr schrieb Thomas <notifications@github.com  
>:  
  
> I added 5 more 10x10 test matrices with numbers at different orders of  
> magnitude. I also changed the test suite so that it now covers the double  
> and float data types. For some reason, my changes aren't showing up when I  
> click on my new commit - any reason why this is?  
>  
> Right now, I'm still a bit uncertain whether the eigensolver can handle  
> complex matrices... AFAIK Eigen has a separate eigensolver specifically for  
> complex and hermitian matrices.  
>  
Maybe we should first compare their (Eigen/Octave) interfaces with the one  
you are proposing. Could you send an email with such a comparison?  
  
I just realized that the eigensolver test suite was never added to the  
> jamfile, so it was never run during build. For the eigensolver test suite  
> and future test suites, do we add it manually to the Jamfile? Until then,  
> we cannot merge anything  
>  
  
Yes. You need to modify the jam files so that it is build and tested as  
well. Also Eigen or Octave needs to be installed for CI.  
  
  
> For the new test cases I added, I also computed the same result in Eigen.  
> Inside benchmarks I could potentially just copy the "Eigen" directory found  
> inside the Eigen package, and then make the comparison there with the ublas  
> eigensolver.  
>  
A copy of eigen solvers within the boost/ublas repo is not a good idea. It  
is okay to install it for testing.  
  
  
> There are also some other minor formatting changes I could do regarding  
> some of the function signatures - adding const specifiers, etc. Some of the  
> comments that rajaditya left also give us some unfinished tasks  
>  
Yes. very good.  
  
  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/pull/68#issuecomment-487319554>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AA6PDJINFF32N6DPNOKHKFTPSS5S3ANCNFSM4HHK3L5Q>  
> .  
>

---

## Comment 9

> Username: thomasyang1207  
> Created_at: 2019-05-05 23:49:50 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-489474150  

Currently, all the tests for the eigensolver (both old ones and new ones) pass. I fixed some minor bugs in the test code and the code for the eigensolver as well.    
  
Right now the test cases are not written using the full extent of the Boost Test Suite. Could I get some guidance on that?  
  
The next step for me is adding benchmarks

---

## Comment 10

> Username: yimyom  
> Created_at: 2019-05-12 11:45:41 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-491588410  

Great job !  
  
On Mon, May 6, 2019 at 9:49 AM Thomas <notifications@github.com> wrote:  
  
> Currently, all the tests for the eigensolver (both old ones and new ones)  
> pass. I fixed some minor bugs in the test code and the code for the  
> eigensolver as well.  
>  
> Right now the test cases are not written using the full extent of the  
> Boost Test Suite. Could I get some guidance on that?  
>  
> The next step for me is adding benchmarks  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/ublas/pull/68#issuecomment-489474150>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AAQOK4A6D7NKCPQQ6UFZXK3PT5XB5ANCNFSM4HHK3L5Q>  
> .  
>

---

## Comment 11

> Username: penguian  
> Created_at: 2019-05-12 13:22:03 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-491595615  

Hello all,  
I have been following this discussion on the mailing list, and have commented   
via Stackoverflow. Please see below.  
All the best, Paul  
  
On Sunday, 12 May 2019 9:45:42 PM AEST David Bellot wrote:  
Great job !  
  
On Mon, May 6, 2019 at 9:49 AM Thomas <notifications@github.com> wrote:  
> Currently, all the tests for the eigensolver (both old ones and new ones)  
> pass. I fixed some minor bugs in the test code and the code for the  
> eigensolver as well.  
> Right now the test cases are not written using the full extent of the  
> Boost Test Suite. Could I get some guidance on that?  
> The next step for me is adding benchmarks  
  
Testing pull request #68 using Glucat   
I have just begun testing uBLAS pr #68, that requests to merge   
thomasyang1207:develop from GSOC 2015 into uBLAS. To do this, I am using a new   
branch of GluCat.   
I am testing using GluCat's test11, which extensively uses transcendental   
functions, some of which involve matrix logarithms and square roots, which in   
turn call the eigensolver to determine the (complex) eigenvalues of the   
matrix. In my experience, the test becomes unacceptably slow the very first   
time that the eigensolver is given a 4 x 4 matrix. I haven't yet diagnosed the   
problem, but possibly the code is copying matrices more than it really needs   
to?   
Also. I have looked at the code itself and have quite a few comments. In this   
question, I will state just one: the code reads as if it uses the word   
"complex" to mean "imaginary", as in splitting a complex number into its real   
and "complex" parts. This will be an unneeded cause of confusion if it is to   
remain in the public interface of the code, and in fact "complex" should be   
changed to "imag" or "imaginary" throughout the code, whenever it is correct   
and appropriate to do so.   
I have more comments, but how can I make them by performing a code review of   
uBLAS pr #68 within the uBLAS repository? Who would have to grant me access? I   
have a background in C++ as well as linear and multilinear algebra, and taught   
numerical linear algebra at ANU 2009-2011,so I am reasonably up to the task,   
if I can find the time.   
Paul Leopardi   
I have tested using the matrix [0 -1 0 0; 1 0 0 0;0 0 0 -1;0 0 1 0] (Matlab   
notation). This produces an infinite loop in schur_decomposition, because the   
matrix argument h to that function has diagonal entries that are 0, and the   
function cannot cope with diagonal entries that are 0 or close to 0. Also, the   
Francis algorithm assumes that the matrix is strictly upper Hessenberg, so in   
this case the matrix should have been split into two 2x2 blocks before being   
sent to schur_decomposition. – Paul Leopardi  
  
PS: In fact the Francis algorithm is known to be prone to failure.  
See LAWN187: Lapack 3.1 xHSEQR: Tuning and Implementation Notes  
--   
Paul Leopardi - https://sites.google.com/site/paulleopardi/

---

## Comment 12

> Username: bassoy  
> Created_at: 2019-05-12 16:40:02 UTC  
> Updated_at: 2019-05-12 16:41:18 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-491610466  

> Hello all,  
> I have been following this discussion on the mailing list, and have  
> commented  
> via Stackoverflow. Please see below.  
> All the best, Paul  
>  
> On Sunday, 12 May 2019 9:45:42 PM AEST David Bellot wrote:  
> Great job !  
>  
> On Mon, May 6, 2019 at 9:49 AM Thomas <notifications@github.com> wrote:  
> > Currently, all the tests for the eigensolver (both old ones and new ones)  
> > pass. I fixed some minor bugs in the test code and the code for the  
> > eigensolver as well.  
> > Right now the test cases are not written using the full extent of the  
> > Boost Test Suite. Could I get some guidance on that?  
> > The next step for me is adding benchmarks  
>  
> Testing pull request #68 using Glucat  
> I have just begun testing uBLAS pr #68, that requests to merge  
> thomasyang1207:develop from GSOC 2015 into uBLAS. To do this, I am using a  
> new  
> branch of GluCat.  
> I am testing using GluCat's test11, which extensively uses transcendental  
> functions, some of which involve matrix logarithms and square roots, which  
> in  
> turn call the eigensolver to determine the (complex) eigenvalues of the  
> matrix. In my experience, the test becomes unacceptably slow the very  
> first  
> time that the eigensolver is given a 4 x 4 matrix. I haven't yet diagnosed  
> the  
> problem, but possibly the code is copying matrices more than it really  
> needs  
> to?  
>  
  
Thanks for pointing this out. Maybe you could be more specific on this by  
referencing a line within the code. It would be easier for Thomas  
to analyze and validate.  
  
  
> Also. I have looked at the code itself and have quite a few comments. In  
> this  
> question, I will state just one: the code reads as if it uses the word  
> "complex" to mean "imaginary", as in splitting a complex number into its  
> real  
> and "complex" parts. This will be an unneeded cause of confusion if it is  
> to  
> remain in the public interface of the code, and in fact "complex" should  
> be  
> changed to "imag" or "imaginary" throughout the code, whenever it is  
> correct  
> and appropriate to do so.  
>  
  
Yes, you are right. Could you again reference the code lines so we that we  
are able to find the culprit lines faster?  
  
  
I have more comments, but how can I make them by performing a code review  
> of  
> uBLAS pr #68 within the uBLAS repository? Who would have to grant me  
> access? I  
> have a background in C++ as well as linear and multilinear algebra, and  
> taught  
> numerical linear algebra at ANU 2009-2011,so I am reasonably up to the  
> task,  
> if I can find the time.  
>  
  
Please feel free to also work on the pull request  (maybe together with  
Thomas if you and Thomas wish).  
  
  
> I have tested using the matrix [0 -1 0 0; 1 0 0 0;0 0 0 -1;0 0 1 0]  
> (Matlab  
> notation). This produces an infinite loop in schur_decomposition, because  
> the  
> matrix argument h to that function has diagonal entries that are 0, and  
> the  
> function cannot cope with diagonal entries that are 0 or close to 0. Also,  
> the  
> Francis algorithm assumes that the matrix is strictly upper Hessenberg, so  
> in  
> this case the matrix should have been split into two 2x2 blocks before  
> being  
> sent to schur_decomposition.  
>  
  
Thanks. This is why we need better test coverage. Thomas could you  
reproduce those problems?  
  
  
>  
> PS: In fact the Francis algorithm is known to be prone to failure.  
> See LAWN187: Lapack 3.1 xHSEQR: Tuning and Implementation Notes  
>

---

## Comment 13

> Username: thomasyang1207  
> Created_at: 2019-05-13 00:45:49 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-491643497  

I'll get going on reproducing this issue with ublas's test suite.   
  
The line about the complex eigenvalues is in the file eigen_solver.hpp, throughout the eigen_solver class definition. I can fix that as well  
  
@penguian I'd love to learn more about the specifics of implementing an eigensolver, since I'm actually quite new to all this. I have added you as a collaborator to my branch

---

## Review 14 [Changes requested]

> Username: penguian  
> Created_at: 2019-05-19 09:04:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/68#pullrequestreview-239224419  

The proposed code is unfinished and contains unused functions which need to be incorporated and tested. See also these [pull requests](https://github.com/thomasyang1207/ublas/pulls) and [branches](https://github.com/thomasyang1207/ublas/branches) of which two are in progress.  
  
For a description of the LAPACK implementation of the multi-shift QR algorithm, as used in (e.g.) `DGEES`, see [Lapack 3.1 xHSEQR: Tuning and Implementation Notes](http://www.netlib.org/lapack/lawnspdf/lawn187.pdf). This uses the double shift algorithm for small matrices.  
  
See also [the equivalent Eigen code](https://github.com/eigenteam/eigen-git-mirror/blob/master/Eigen/src/Eigenvalues/).

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
  34 |+ 			*/
  35 |+ 			template<class M>
  36 |+ 			void schur_decomposition(M &h) {
```

> Username: penguian  
> Created_at: 2019-05-19 07:29:48 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285367075  

This function is incomplete as a practical implementation of the Francis Double Shift QR Algorithm. [1, p. 173]:  "In practice, the QR iteration with the Francis shift can fail to converge ... So the practical algorithm in use for decades had an "exceptional shift" every 10 shifts if convergence had not occurred. ... another exceptional shift was recently added to the algorithm."   
  
[1] James Demmel, Applied Numerical Linear Algebra, SIAM, 1997.

---

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
 278 |+ 
 279 |+ 			template<class M>
 280 |+ 			void find_small_diag_entry(M &h, typename M::size_type end, typename M::value_type l1_norm, typename M::size_type &small_index)
```

> Username: penguian  
> Created_at: 2019-05-19 07:30:11 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285367088  

Unused function. See the equivalent [Eigen](https://github.com/eigenteam/eigen-git-mirror/blob/master/Eigen/src/Eigenvalues/RealSchur.h) method `RealSchur<MatrixType>::findSmallSubdiagEntry` which is used in `RealSchur<MatrixType>::computeFromHessenberg`. This function should be used in `schur_decomposition` above, but is not.

---

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
 297 |+ 
 298 |+ 			template<class M>
 299 |+ 			void row_split(M &h, M &qv, typename M::size_type end, typename M::value_type exceptional_shift_sum)
```

> Username: penguian  
> Created_at: 2019-05-19 07:31:35 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285367140  

Unused function. See the equivalent [Eigen](https://github.com/eigenteam/eigen-git-mirror/blob/master/Eigen/src/Eigenvalues/RealSchur.h) method `RealSchur<MatrixType>::splitOffTwoRows` which is used in `RealSchur<MatrixType>::computeFromHessenberg`. This function should be used in `schur_decomposition` above, but is not.

---

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
 366 |+ 
 367 |+ 			template<class M>
 368 |+ 			void infer_shifts(M &h, typename M::size_type end, typename M::size_type iter_nos, typename M::value_type exceptional_shift_sum, vector<typename M::value_type> &shift_vector)
```

> Username: penguian  
> Created_at: 2019-05-19 07:35:10 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285367256  

Unused function. See the equivalent [Eigen](https://github.com/eigenteam/eigen-git-mirror/blob/master/Eigen/src/Eigenvalues/RealSchur.h) method `RealSchur<MatrixType>::computeShift` which is used in `RealSchur<MatrixType>::computeFromHessenberg`. This function should be used in `schur_decomposition` above, but is not.  
  
This is the function that contains the exceptional shifts that I mentioned above at line 36. See below.

---

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
 379 |+ 				//Original Shift 
 380 |+ 				if (iter_nos == size_type(10))
 381 |+ 				{
```

> Username: penguian  
> Created_at: 2019-05-19 07:36:20 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285367300  

This is "Wilkinson's original ad hoc shift", as per the equivalent Eigen method RealSchur<MatrixType>::computeShift.

---

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
 416 |+ 
 417 |+ 			template<class M>
 418 |+ 			void francis_qr_step(M &h, M &qv, typename M::size_type rowStart, typename M::size_type end, vector<typename M::value_type> shifts)
```

> Username: penguian  
> Created_at: 2019-05-19 08:01:33 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285368140  

Unused function. See the equivalent Eigen methods RealSchur<MatrixType>::initFrancisQRStep and RealSchur<MatrixType>::performFrancisQRStep which are used in RealSchur<MatrixType>::computeFromHessenberg. This function should be used in schur_decomposition above, but is not.

---

📁 include/boost/numeric/ublas/schur_decomposition.hpp

```diff
 393 |+ 				// Matlabs ad hoc shift (somehow Eigen people got this and its not in public domain)
 394 |+ 				// Sometimes I just think Numerical Computing is a big insider job
 395 |+ 				if (iter_nos == size_type(30))
```

> Username: penguian  
> Created_at: 2019-05-19 08:17:04 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285368696  

This is "MATLAB's new ad hoc shift", as per the equivalent Eigen method RealSchur<MatrixType>::computeShift.


📁 include/boost/numeric/ublas/eigen_solver.hpp

```diff
 102 |+ 
 103 |+ 		size_type n = real_schur_form.size1();
 104 |+ 		size_type i = size_type(0);
```

> Username: penguian  
> Created_at: 2019-05-19 08:20:08 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285368803  

It is generally unnecessary to cast small `int` constants to `size_type` and it clutters the source code. The exception may be for `std::max` and `std::min` but there both sides might be cast to (e.g.) `long`.

---

📁 include/boost/numeric/ublas/eigen_solver.hpp

```diff
  34 |+ class eigen_solver {
  35 |+ private:
  36 |+ 	M matrix;
```

> Username: penguian  
> Created_at: 2019-05-19 08:27:41 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369129  

Most of the source files retain tab characters, which automatically translate to 8 spaces when displayed here. These tabs should be replaced by 4 spaces.

> Username: penguian  
> Created_at: 2019-05-19 08:44:57 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369820  

See branch [clean-indentation](https://github.com/thomasyang1207/ublas/tree/clean-indentation) (in progress).

---

📁 include/boost/numeric/ublas/eigen_solver.hpp

```diff
  42 |+ 	bool has_eigenvectors;
  43 |+ 	M eigenvalues_real;
  44 |+ 	M eigenvalues_complex;
```

> Username: penguian  
> Created_at: 2019-05-19 08:36:48 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369493  

The term `complex` as used here and elsewhere is misleading and should be replaced by `imag` or `imaginary. See pull request [API: Change misleading instances of complex to imag or imaginary](https://github.com/thomasyang1207/ublas/pull/1).

---

📁 include/boost/numeric/ublas/eigen_solver.hpp

```diff
 318 |+ 		}
 319 |+ 
 320 |+ 		for (size_type j = n; j--!=size_type(0);)
```

> Username: penguian  
> Created_at: 2019-05-19 08:41:11 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369676  

It would be better to have  
```  
for (size_type j = n - 1; j != 0; j--)  
```  
if that is what is meant.


📁 test/common/testhelper.hpp

```diff
  79 |+ template < class M1, class M2 >
  80 |+ bool compare_on_tolerance(const boost::numeric::ublas::matrix_expression<M1> & m1,
  81 |+ 	const boost::numeric::ublas::matrix_expression<M2> & m2, double tolerance = 1.0e7) {
```

> Username: penguian  
> Created_at: 2019-05-19 08:22:28 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285368893  

The value of `tolerance` used here is huge, and causes tests to spuriously pass.


📁 test/test_eigensolver.cpp

```diff
  19 |+ using std::string;
  20 |+ 
  21 |+ static const string matrix_IN[] = { "[3,3]((-149.0,-50.0,-154.0),(537.0,180.0,546.0),(-27.0,-9.0,-25.0))\0",
```

> Username: penguian  
> Created_at: 2019-05-19 08:25:53 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369055  

Formatting a string by placing it on a single long line is unnecessary in C++, since constant strings can be defined  
```  
"like "  
"so"  
```  
which produces the string "like so".  
The matrices would be more readable if they had one line per matrix row.

> Username: penguian  
> Created_at: 2019-05-19 08:43:51 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369773  

See branch [more-eigensolver-tests](https://github.com/thomasyang1207/ublas/tree/more-eigensolver-tests) (in progess).

> Username: penguian  
> Created_at: 2019-05-19 14:03:02 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285382099  

The extra NULL character `\0` at the end of the string is not needed.

---

📁 test/test_eigensolver.cpp

```diff
 111 |+ 		MATRIX Lambda_Complex(3, 3);
 112 |+ 
 113 |+ 		for (int i = 0; i < 3; i++){
```

> Username: penguian  
> Created_at: 2019-05-19 08:34:13 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369392  

This tests only the first 3 eigenvalues, instead of all eigenvalues. See pull request [Test all eigs](https://github.com/thomasyang1207/ublas/pull/2).

---

📁 test/test_eigensolver.cpp

```diff
  30 |+ 																		"[10,10]((0, 2e+10, 3e+10, 4e+10, 3e+10, 6e+10, 7e+10, 8e+10, 9e+10, 1e+11),(1e+10, 2e+10, 3e+10, 3e+10, 5e+10, 0, 7e+10, 8e+10, 9e+10, 1e+11),(3e+10, 2e+10, 0, 4.2e+10, 5e+10, 6.1e+10, 0, 8e+10, 9e+10, 0),(1e+10, 2e+10, 3e+10, 4e+10, 5e+10, 6e+10, 0, 8e+10, 9e+10, 1e+10),(1e+10, 2e+10, 3e+10, 4.5e+10, 5e+10, 6e+10, 7e+10, 8e+10, 9e+10, 0),(5e+10, 6e+10, 0, 4e+10, 5e+10, 6e+10, 7e+10, 8e+10, 9e+10, 1e+10),(0, 2e+10, 3e+10, 0, 5e+10, 6e+10, 7e+10, 1e+10, 9e+10, 4e+10),(1e+10, 2e+10, 0, 4e+10, 5e+10, 6e+10, 7e+10, 8e+10, 9.3e+10, 0),(0, 2e+10, 3e+10, 4e+10, 0, 6e+10, 7e+10, 8e+10, 9e+10, 1e+11),(0, 2e+10, 3e+10, 4e+10, 5e+10, 6e+10, 7e+10, 8e+10, 9e+10, 0))\0"
  31 |+ 																	};
  32 |+ 
```

> Username: penguian  
> Created_at: 2019-05-19 08:46:07 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369866  

More tests are needed, including tests which cause the current implementation to fail to converge.

> Username: penguian  
> Created_at: 2019-05-19 08:47:01 UTC  
> Updated_at: 2019-05-19 09:04:58 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285369903  

See branch [more-eigensolver-tests](https://github.com/thomasyang1207/ublas/tree/more-eigensolver-tests) (in progess).


---

## Review 15 [Commented]

> Username: penguian  
> Created_at: 2019-05-19 13:19:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/68#pullrequestreview-239237577  

📁 include/boost/numeric/ublas/eigen_solver.hpp

```diff
 104 |+ 		size_type i = size_type(0);
 105 |+ 
 106 |+ 		eigenvalues_real = zero_matrix<value_type>(n,n);
```

> Username: penguian  
> Created_at: 2019-05-19 13:19:01 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285380143  

The real and imaginary components of the eigenvalues should be returned as vectors, especially when eigenvectors are not required. Compare with [Eigen](https://github.com/eigenteam/eigen-git-mirror/blob/master/Eigen/src/Eigenvalues/EigenSolver.h).


---

## Review 16 [Commented]

> Username: penguian  
> Created_at: 2019-05-19 14:04:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/68#pullrequestreview-239239443  

📁 test/test_eigensolver.cpp

```diff
 132 |+ 
 133 |+ 	if (!_fail_counter) {
 134 |+ 		std::cout << "\nEigen Solver regression suite passed.\n";
```

> Username: penguian  
> Created_at: 2019-05-19 14:04:37 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285382183  

Usually in C++, end of line is indicated by `std::endl`. The use of `"\n"` here is C usage.


---

## Review 17 [Commented]

> Username: penguian  
> Created_at: 2019-05-19 14:09:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/68#pullrequestreview-239239632  

📁 test/test_eigensolver.cpp

```diff
 126 |+ 		}
 127 |+ 
 128 |+ 		assertTrue("Real portion of verifier is zero:", compare_on_tolerance(Lambda_Real, Zero_Matrix));
```

> Username: penguian  
> Created_at: 2019-05-19 14:09:16 UTC  
> Updated_at: 2019-05-19 14:09:17 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285382390  

From line 121 to here: why not just compare (e.g.) `norm _2(Lambda)/norm_2(prod(V,D))` with a small tolerance?


---

## Review 18 [Commented]

> Username: penguian  
> Created_at: 2019-05-19 14:12:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/68#pullrequestreview-239239780  

📁 test/test_eigensolver.cpp

```diff
 118 |+ 			} 
 119 |+ 		}
 120 |+ 		Lambda = prod(M, V) - prod(V, D);
```

> Username: penguian  
> Created_at: 2019-05-19 14:12:10 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r285382571  

The name `Lambda` here is misleading, because in this context, the letter `lambda` is used to denote a generic eigenvalue of a matrix. Maybe use `Verifier`, since that is what the message below calls it.


---

## Review 19 [Commented]

> Username: penguian  
> Created_at: 2019-05-26 11:04:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ublas/pull/68#pullrequestreview-242034446  

📁 include/boost/numeric/ublas/eigen_solver.hpp

```diff
 307 |+ 					value_type t = (std::max)((std::abs)(T(i, k - size_type(1))), (std::abs)(T(i, k)));
 308 |+ 					if ((std::numeric_limits<value_type>::epsilon() * t)*t > value_type(1)) {
 309 |+ 						for (size_type j = i; j < k + i - size_type(1); j++){
```

> Username: penguian  
> Created_at: 2019-05-26 11:04:50 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#discussion_r287592814  

The bound of `k + i - size_type(1)` here is incorrect and will cause `j` to index past the end of the matrix. Compare with [EigenSolver.h](https://eigen.tuxfamily.org/dox/EigenSolver_8h_source.html) lines 596-598.  
See [thomasyang1207 pull request #5](https://github.com/thomasyang1207/ublas/pull/5) for the fix.


---

## Comment 20

> Username: penguian  
> Created_at: 2019-05-26 12:19:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/68#issuecomment-495995191  

Hi @thomasyang1207 . I have commented on this merge request and have made suggested changes of my own. See the branches and merge requests at https://github.com/thomasyang1207/ublas  
I have done about as much as I can without diving in to the eigensolver and Schur code to properly complete it. I now need to spend some extra time at work and will have little time to work on this pull request for at least 4 weeks.  
  
In the meantime, you can study [the Eigenvalues module of Eigen](https://eigen.tuxfamily.org/dox/group__Eigenvalues__Module.html) and read  
  
[1] Demmel, Applied Numerical Linear Algebra, SIAM, 1997. Chapter 4 Nonsymmetric Eigenvalue Problems. (Section 4.4.8 covers QR iteration with implicit shifts, but it would be best to read from the start of the chapter.)  
  
[2] Trefethen and Bau. Numerical Linear Algebra, SIAM 1997, Part V Eigenvalues. (Lecture 29 covers the QR algorithm with shifts, but it would be best to read from Lecture 24.)

---
