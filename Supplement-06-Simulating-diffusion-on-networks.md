# Diffusion on a network
This code simulates a discrete-time spreading process on a network using the graph Laplacian. *Note*: 
This script will write files to your computer. Do not run if this is an issue.

```r
## Load the following libraries (install each of these if you've never used them before)
library(igraph)
library(RColorBrewer)
library(viridis)
library(ggplot2)

## Make a directory to store our images
getwd()                         ## show your current working directory
dir.create('./animationFrames') ## create a new folder called "animationFrames in your current working directory

## Make a lattice and take a look
G  = make_lattice(dimvector = c(32, 64), circular = TRUE)
lo = layout_with_fr(G, dim = 3)
plot(G, vertex.label = NA, vertex.size = 4, vertex.color = '#f7f7f7', edge.color = '#000000', layout = lo)

## Get the corresponding graph Laplacian
A = as.matrix(get.adjacency(G))
D = A * 0
diag(D) = degree(G)
L = D - A
L = L / sqrt(diag(L)) ## row-normalize for our simulation

## Color the network based on sticking heat somewhere random
my_resolution = 11
my_palette    = colorRampPalette(inferno(my_resolution))
curr    = as.matrix(L[, 1] * 0)
curr[sample(1:nrow(L), size = 1)] = 1

## Init our simulation
time    = seq(1, 500, by = 1)
curr    = curr - 0.1*L %*% curr
my_vector = curr
my_colors = my_palette(my_resolution)[as.numeric(cut(curr, breaks = my_resolution))]
plot(G, vertex.label = NA, vertex.size = 5, vertex.color = my_colors, edge.color = '#000000', layout = lo)

## Simulate for t timesteps and save the figure for each time step
for(t in time){
  curr    = curr - 0.1*L %*% curr
  my_vector = curr
  my_colors = my_palette(my_resolution)[as.numeric(cut(curr, breaks = my_resolution))]
  png(file = paste0("./animationFrames/graph_", stringr::str_pad(t, 5, pad = "0"), ".png"))
  plot(G, vertex.label = NA, vertex.size = 5, vertex.color = my_colors, edge.color = '#000000', layout = lo)
  dev.off()
}
```

The end result will be a folder full of movie frames. These can be stitched into an animated `.gif` using software like [ImageMagick](https://imagemagick.org/index.php).
