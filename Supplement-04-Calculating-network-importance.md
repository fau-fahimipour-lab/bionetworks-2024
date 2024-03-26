# Spectral centrality

The following code block will calculate node importance for a random geometric graph based on our spectral centrality measure from class.

```r
library(igraph)

## Make a random geometric graph
G = grg.game(200, 0.15)
lo = layout_with_kk(G)
plot(G, 
     vertex.label = NA, vertex.color = "#f7f7f7", vertex.size = 5, 
     edge.width = 0.25, edge.color = "#000000", edge.curved = 0.05,
     layout = lo)

## Get A
A = as.matrix(get.adjacency(G))

## Get eigenvalues/vectors
importances = eigen(A)$vectors[, 1] / eigen(A)$vectors[, 1][which.max(abs(eigen(A)$vectors[, 1]))]

## Get node colors matching importance values
res = 25
my_palette = colorRampPalette(viridis::mako(res))
my_colors  = my_palette(res)[as.numeric(cut(importances, breaks = res))]

## Plot network with importances
plot(G, 
     vertex.label = NA, vertex.color = my_colors, vertex.size = 7, 
     edge.width = 0.25, edge.color = "#000000", edge.curved = 0.05,
     layout = lo)
```



