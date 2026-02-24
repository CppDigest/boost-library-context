# #53 - Event-based integration [Open]

> Username: Carbohz  
> Created at: 2022-07-29 14:55:37 UTC  
> Updated at: 2024-06-29 22:23:46 UTC  
> Url: https://github.com/boostorg/odeint/issues/53  

Hi!  
  
Does odeint support event-based integration?   
Sometimes it is useful to stop not on specific finish time, but discrete event, when stop condition of the integrator is not defined by the time t but by a target condition on state y (say y[0]=1.0 for example).  
Some open-source libraries provide this functionality, like hipparchus(Java): https://www.hipparchus.org/hipparchus-ode/#Discrete_Events_Handling

---

## Comment 1

> Username: csb2024  
> Created at: 2024-06-29 22:23:45 UTC  
> Url: https://github.com/boostorg/odeint/issues/53#issuecomment-2198361659  

odeint Is there a way to implement matlab's ode45 event mechanism such as the following code:  
tspan=[0 t_max]; %Time span of simulation in sec  
Opt = odeset('Events', @myevent);  
[t,x]=ode45(@EOM,tspan,x0,Opt); function [value, isterminal]; %Time span of simulation in sec  
  
function [value, isterminal, direction] = myEvent(t, y)  
%value = (y(11) < 0);  
value = y(11); isterminal = 1; % myEvent(t, y)  
isterminal = 1; % Stop the integration  
direction = -1; end  
end  
  
Translated with DeepL.com (free version)
