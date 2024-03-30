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

# A food chain example

The following code simulates dynamics of a continuous-time food chain model, where the consumers follow type-II functional response interaction dynamics. 
We derived this model in Lecture 15 - *Coarse-graining*.

Make sure you install the required libraries before running the code. At the end, you should see a plot of resource, herbivore, and top predator time-series.

```r
## Load the following libraries (install each of these if you've never used them before)
library(deSolve)  ## This is for solving ODEs
library(reshape2) ## This is for converting data between formats for plotting
library(ggplot2)  ## This is for making pretty plots

## Define the food chain model according to R/deSolve syntax
foodChainModel = function(time, state, parameters) {
  ## Extract the state variables
  R = state['R'] ## Resources
  H = state['H'] ## Herbivores
  P = state['P'] ## Predators
  ## Extract the model parameters
  a = parameters['a'] ## Herbivory rate by herbivores, H
  b = parameters['b'] ## Predation rate by predators, P
  f = parameters['f'] ## Transfer efficiency, how many units of H must be eaten to make a unit of P
  m = parameters['m'] ## Natural death rate of predators, P
  ## Define the equations __HERE__
  dR = R*(1 - R) - R*H / (1 + R)
  dH = a*R*H / (1 + R) - H - b*H*P / (1 + H)
  dP = f*b*H*P / (1 + H) - m*P
  # Return the derivatives as a list
  return(list(c(dR, dH, dP)))
}

## Define the initial conditions and model parameters
initialState = c('R' = 1e-1, 'H' = 1e-2, 'P' = 1e-3)
parameters   = c('a' = 5, 'b' = 3, 'f' = 0.5, 'm' = 0.6)

## Define the time span to simulate
timeSpan = seq(0, 250, by = 0.01)

## Pass these to the ODE solver from deSolve
results = as.data.frame(ode(y = initialState, times = timeSpan, func = foodChainModel, parms = parameters, method = 'lsoda'))

## Convert my results to long format for plotting
resultsClean = melt(results, id.vars = 'time')

## Plot the results with ggplot, make the time series of each species a different color
ggplot(data = resultsClean, aes(x = time, y = value, group = variable, colour = variable)) +
  theme_classic() +
  xlab('Time') +
  ylab('Abundance') +
  geom_path(size = 0.65) +
  scale_colour_brewer(palette = 'Dark2', name = NULL)
```

