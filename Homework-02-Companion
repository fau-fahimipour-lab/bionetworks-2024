# Simulating a simple epidemic
This script contains minor modifications of the network simulation we built in class together.

First thing to do is download the Les Miserables social network data set and load it into `R` as an `igraph` object.
The data can be found on [Mark Newman's website](http://www-personal.umich.edu/~mejn/netdata/). **Note:** Inside the `read.graph` function,
we tell R the location of the `.gml` file we downloaded. Mine happens to be in my `/Downloads/` folder, but yours may be in a different location.
You will need to modify the file path inside the quotes in order to point to the correct location on your specific machine.
For example, if you download the file and put it on your desktop, it might look something like `"/Users/YourName/Desktop/lesmis.gml"`.

```r
## Load the igraph package
library(igraph)

## Import the Les Mis network
G = read.graph('~/Downloads/lesmis/lesmis.gml', format = 'gml')

## Grab the adjacency matrix, we will need this later...
A = as.matrix(get.adjacency(G))

## Also remember that the number of rows in the adjacency is equal to the number of nodes
nrow(A)

## Further remember that the total number of 1s in the adjacency equals 2 times the number of links
sum(A) / 2

## Set the layout for the graph plot
myLayout = layout_with_fr(G)

## Plot the network to have a look
plot(G,
     vertex.label.cex = 0.45, vertex.label.color = '#000000',
     vertex.size = 8, vertex.color = '#f7f7f7',
     edge.color = '#000000', edge.width = 1, edge.curved = 0.15,
     layout = myLayout)
```

Now you will want to load the following functions. Let me describe what they do
The first function, `updateStates` takes in [1] an adjacency matrix `A`,
[2] a vector of node states `nodeStates` in the current time step,
[3] an infection rate parameter `p`, which I have set to 0.45 here,
and [4] a recovery rate parameter `r`, which I have set to 0.15.
The function loops through each node in the network (person) and does the following:
If they are already infected, flip a biased coin with probability of heads = 0.045.
If you get heads, the node recovers from the infection.
If they are not infected, search their social links, and check the states of their
neighbors. For each neighbor that is infected, flip a biased coin with probability
of heads = 0.015 and infect if it lands on heads. Do this for all nodes to spit out the
updated states for the next time step.

The second function repeats this step for `simulationTime` time steps, and
returns the final fraction of infected nodes.

```r
## -----
## Now let's simulate spreading disease on the social network
## -----

## Update my states for -- one time step --
updateStates = function(A, nodeStates, p = 0.15, r = 0.45){
  ## A blank vector to fill in my next states
  newStates = rep(NA, length(nodeStates))
  ## Loop through each node in the network
  for(i in 1:nrow(A)){
    ## -
    ## Recovery 
    ## -
    ## If the node is already infected, they recover with probability r
    if(nodeStates[i] == 1){
      ## Flip a coin with probability of heads = r. If heads, recover.
      coinFlip     = rbinom(n = 1, size = 1, prob = 1 - r)
      newStates[i] = coinFlip
    } else{
      ## -
      ## Infection 
      ## -
      ## Which nodes are my neighbors?
      neighbors = which(A[i, ] == 1)
      ## What are their current states?
      neighborStates = nodeStates[neighbors]
      ## How many infected neighbors do I have?
      nInfectedNeigh = sum(neighborStates)
      ## Flip nInfectedNeigh coins and if any are heads, infect me
      newStates[i]   = 1 * (any(rbinom(n = nInfectedNeigh, size = 1, prob = p) == 1))
    }
  }
  ## Return my new states
  newStates
}

## One whole simulation by stringing together many time steps
simulateOneNetwork = function(A, initialFractionInfected, simulationTime){
  ## Let's assign a random state to each node to start
  nodeStates = runif(nrow(A))
  nodeStates = ifelse(nodeStates < initialFractionInfected, 1, 0)
  ## Compute the mean degree of my starting nodes
  meanDegreeStarters = mean(rowSums(A)[nodeStates == 1])
  ## Update the states in time
  for(t in 1:simulationTime){
    ## Update once
    nodeStates = updateStates(A, nodeStates)
  }
  ## Calculate the fraction of infected nodes
  fractionInfectedNodes = mean(nodeStates)
  ## Return the final infected
  fractionInfectedNodes
}
```

Now I want to run 500 iterations of this simulation, save the results, and plot them.
I will plot them as a histogram, which shows the distribution of final infection states
across all of my simulations. I've marked the average with a red line.

```r
## It works! Now I want to perform N simulations.
## First it is always good practice to allocate an empty
## array that you will fill in
## NOTE: This might take a few minutes to run
nSimulations = 500
results = data.frame(
  'initialFraction'    = rep(0.1, nSimulations),
  'finalFraction'      = rep(NA)
)

## Fill in my results DF
for(r in 1:nrow(results)){
  oneSimulationResult           = simulateOneNetwork(A, initialFractionInfected = results$initialFraction[r], simulationTime = 100)
  results$finalFraction[r]      = oneSimulationResult
}

## Plot the distribution of final infection spread for all your simulations
hist(results$finalFraction, breaks = 15)

## What is the average final infection state for all 500 simulations?
mean(results$finalFraction)
abline(v = mean(results$finalFraction), col = 'darkred', lwd = 2)
```
