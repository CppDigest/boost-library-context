# #25 Enable the adaption of the maximal step size of dense output steppers [Closed]

> Username: friedrichatgc  
> Created at: 2017-12-28 17:00:31 UTC  
> Updated at: 2018-01-05 20:25:36 UTC  
> Closed at: 2018-01-05 20:25:36 UTC  
> Url: https://github.com/boostorg/odeint/pull/25  

For efficient simulation of "hybrid" systems the integrator must approach  
the sample points where the discrete variables change their value.  
  
(hybrid systems = systems of ODEs which include discrete variables, beeing  
internal variables of the system which only change their value at discrete  
sample points)  
  
Approaching sample points can be done by adapting the maximal integrator  
step size to min(max_step_size, next_sample_point_time - current_time)  
before each do_step.  
  
To achive this in odeint for all dense output steppers the following  
changes must be done (which does not change the existing API):  
- make private members in bulirsch_stoer_dense_out,  
  default_step_adjuster and rosenbrock4_controller protected.  
- allow std::ref/boost::ref for step_adjuster in controlled_runge_kutta  
  and controlled_runge_kutta and for stepper in rosenbrock4_dense_output  
  by unwrapping these before use.  
This allows to pass the step adjusters by reference to the dense output  
steppers which than allows to change the maximal step size (in the step  
adjuster) before each call to do_step.

---

## Comment 1

> Username: mariomulansky  
> Created_at: 2018-01-05 16:37:15 UTC  
> Url: https://github.com/boostorg/odeint/pull/25#issuecomment-355601003  

Thanks for these PRs, from the description it looks very useful - I haven't had time to check the code yet. However, I was wondering if you could create these PRs against this repo instead: https://github.com/headmyshoulder/odeint-v2 (master). This is our development repo where we also have a travis CI set up. For such bigger changes we prefer to have them run through travis. Sorry for the inconvenience, it's a bit annoying that odeint lives in two repos...

---

## Comment 2

> Username: friedrichatgc  
> Created_at: 2018-01-05 20:25:36 UTC  
> Url: https://github.com/boostorg/odeint/pull/25#issuecomment-355656582  

OK. I was already surprised why Travis CI was not working/enabled on boost/odeint. That's why I added as first commit on each PR the Jamroot file from headmyshoulder/odeint-v2 and enabled Travis on friedrichatgc/odeint to already check the PRs, see e.g. https://travis-ci.org/friedrichatgc/odeint/builds/322915574?utm_source=github_status&utm_medium=notification  
  
I will reopen the PRs on headmyshoulder/odeint-v2 next time.

---
