# Interacting with networks in R with igraph
Previously we went over the notion of functions in programming. 
In R, there are things called "libraries" and these are collections of functions written by various people across the world.
Chances are, if you want to do something in R, there is already a library that can do it.
In this case, we will use one of the most popular libraries for manipulating and visualizing networks: "igraph"

```r
## Install the igraph library if you never have before
install.packages("igraph")
```
This will take a little bit of time, to download the pieces of software from the web and install then in your R instance.
After this is done, to load the library we just need to evaluate:


```r
## Install the igraph library if you never have before
library("igraph")
```

Now we want to visualize a network. The problem is, what network will we choose? Let's start by creating a random one.


```r
## First we will make a graph from scratch
## We need to define an adjacency matrix
## Let's first define the dimensions of the matrix, i.e. how many nodes we have
N = 100
```

I have indicated here that we will start with N = 100 nodes in our network. 
We want to encode our network as an adjacency matrix.

```r
## Now we will populate a matrix with 100 rows and 100 columns with random numbers.
## The N^2 random numbers will be drawn from a log-normal distribution.
A = matrix(rlnorm(N^2), nrow = N)

## Verify that I have the right size matrix
dim(A)
```

You can take a look at some other random number distributions like this: 
`hist(rnorm(1000), breaks = 20)`, `hist(runif(1000), breaks = 20)`, `hist(rlnorm(1000), breaks = 20)`

```r
## Right now we have a matrix R full of random numbers between 1 and 0.
## Let's turn this into an adjacency matrix by simply defining a cutoff.
## Random numbers below this cutoff will be 1s, and above will be 0s
cutoff        = 1
A[A < cutoff] = 0 ## this line is saying take A, grab all of the entries where A < my cutoff, and set those to 0

## We also will ignore loops for now, which we can do by making the diagonal entries 0
diag(A) = 0

## Let's also give some labels to our nodes
rownames(A) = colnames(A) = paste('Node', 1:N, sep = '_')
```

Now that we have a matrix that represents some random network, we can pass it to `igraph`

```r
## Now I need to turn my adjacency matrix into an igraph object
G = graph_from_adjacency_matrix(A, mode = 'undirected', weighted = TRUE)

## We can extract the node set and link set like this:
V(G)
E(G)
```

Now we want to look at our network. Try to evaluate these one by one and figure out what each new argument is doing

```r
## Plot it
plot(G)

## A few extra bits
plot(G, vertex.label = NA)
plot(G, vertex.label = NA, vertex.size = 5)
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253')
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253', edge.width = 2)
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253', edge.width = 0.25*E(G)$weight)
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253', edge.width = 0.25*E(G)$weight, edge.color = '#000000')
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253', edge.width = 0.25*E(G)$weight, edge.color = '#000000', layout = layout_on_grid(G))
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253', edge.width = 0.25*E(G)$weight, edge.color = '#000000', layout = layout_with_kk(G))
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253', edge.width = 0.25*E(G)$weight, edge.color = '#000000', layout = layout_with_fr(G))
```

Finally, deploying the algorithms we have learned so far is very easy.

```r
## The function `distances` returns a matrix where the entries are walk lengths between pairs of nodes
D = distances(G, algorithm = 'dijkstra', weights = NA)

## Which we can glance at using a heatmap
heatmap(D)

## The minimum spanning tree function is calles `mst()`
tree = mst(G, weights = E(G)$weight)
plot(tree)

## Let's compare the full network to the tree. First we need to fix the positions of our nodes
fixedLayout = layout_with_fr(G)

## Visualize the full graph and the tree
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253',
     edge.width = 0.05*E(G)$weight, edge.color = '#000000',
     layout = fixedLayout)

plot(tree, vertex.label = NA, vertex.size = 5, vertex.color = '#2f9253',
     edge.width = 0.25*E(G)$weight, edge.color = '#000000',
     layout = fixedLayout)
```
