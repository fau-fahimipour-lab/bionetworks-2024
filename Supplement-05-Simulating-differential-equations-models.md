The following code block does 2 things. The first function, `sisModel` contains the pieces you need to simulate a continuous-time ODE model. 
To simulate (i.e., numerically integrate), we use the `deSolve` library, which is contains several high-performance ODE-solvers.
Note: you may need to first install this library using `install.packages("deSolve")`.

```r
## Load library
library(deSolve)

## Define the SIS model
sisModel = function(time, state, parameters) {
  
  ## Extract the state variable
  I = state['I']
  
  ## Extract the model parameters
  r = parameters['r']
  p = parameters['p']
  z = parameters['z']
  
  ## Define the ODEs
  dI = -r*I + p*z*I*(1 - I)
  
  ## Return the derivatives as a list
  return(list(c(dI)))
}
```

The ODE solver expects (1) a vector of times, (2) a vector of our state variables, and (3) a vector of parameters.
The next function simply calculates the steady state of our model, based on the solution from Lecture 12.

```r
## SIS equilibrium
sisEquilibrium = function(parameters){
  
  ## Extract the model parameters
  r = parameters['r']
  p = parameters['p']
  z = parameters['z']
  
  ## Calculate steady state
  ss = 1 - r / (p * z)
  ss
}
```

We can simulate our model (i.e., run `sisModel`), and also mark the expected steady state (the output of `sisEquilibrium`), 
and show them on the same plot as follows:

```r
## Define the initial conditions and model parameters in labelled vectors
initialState = c('I' = 0.01)
parameters   = c('r' = 2, 'p' = 1, 'z' = 3)

## Define the time span
timeSpan = seq(0, 15, by = 0.01)

## Calculate the equilibrium point
eq = sisEquilibrium(parameters)

## Solve the ODE model
results = ode(y = initialState, times = timeSpan, func = sisModel, parms = parameters, method = 'lsoda')

## Plot the results
plot(results, type = "l", lwd = 1, xlab = "Time", ylab = "Proportion Infected", bty = "l")
abline(h = eq, col = 'darkred', lwd = 2, lty = 3)
```

You should see a time series of the simulation, with a horizontal red line marking the expected steady-state.
You'll see that they match, confirming our calculation!

