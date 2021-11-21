# Clustering data

For this activity, we will look at some data about three species of iris flowers. The data includes 
measurements for the length and width of the petals and sepals for a set of sample flowers. It also 
identifies which species each sample belongs to. As such we can already divide the data into three 
known groups. However, this is not always possible with data or we may have a new selection of samples 
for which we don’t know their species. One solution is to try and identify clusters of similar records 
or observations in the data. Ideally, those clusters will correspond to the known species.


```{r}
library(knitr)
```

```{r}
#library(gridExtra)
#library(grid)
# preload data + packages
library(ggplot2)
```

```{r}
library(tidyverse)
```

```{r}
head(iris)
```

Look at a summary() of the data, then a generalised pair plot.

```{r}
summary(iris)
```


```{r}
library(GGally)
```

```{r}
iris %>% 
  select(Sepal.Length, Sepal.Width, Petal.Length, Petal.Width, Species) %>% 
  drop_na() %>% 
  ggpairs()
```

  * What do these plots suggest about what measurements are commonly found in each species


# Correlations
The above plots suggest that some of the attribute values may be correlated. This may help indicate
the species. Plot the petal width and length, with the species being indicated by colour and shape.


```{r}
ggplot(iris, aes(Petal.Length, Petal.Width, col=Species, shape=Species)) + 
  geom_point() + 
  ggtitle("Iris scatter plot - petals")
```

  * How could you evaluate the correlation between the petal length and width?
    
  * Is there a clear distinction in the values being plot between the species?

    Yes

# Clustering the data

While we know the species for this data, it would be helpful if we could just identify a cluster for each species.

The k-means algorithm works by generating an initial centroid plot for each of the k clusters, then 
identifies which plots are closest to which centroid (an alternate approach is to randomly assign the 
plots to a cluster). Each centroid is then recalculated using the mean values of the cluster’s population 
and the population is recalculated, until there is a stable population for each cluster.




```{r}
set.seed(55)
cluster.iris <- kmeans(iris[, 3:4], 3, iter.max = 10, nstart = 10)
cluster.iris
```

The above script uses k-means to generate 3 clusters. No more than 10 iterations are allowed. 
nstart allows it to have various random starts and select the one with the lowest variation 
within the clusters.

  * What is the significance of the cluster means?
  * Why do you think a iteration limit was set if the algorithm will stop once the population is stable?
  * Do you get the same cluster populations if you set a different seed or change nstart? Why?
  * What if you change or remove the iteration limit?

Next we need to check whether the clusters are matching the species.


```{r}
table(cluster.iris$cluster, iris$Species)
```

```{r}
cluster.iris$cluster
```

```{r}
iris$Species
```


We can also plot this using the shape to indicate the species, but the colour for the cluster.

The following code chunk is partially complete. Fill out the missing parts (???) and then run.

```{r}
cluster.iris$cluster <- as.factor(cluster.iris$cluster)
ggplot(iris, aes(Petal.Length, Petal.Width, color=cluster.iris$cluster, shape=Species)) + 
  geom_point() + 
  ggtitle("iris cluster - petal vs species")
```

  * How accurate is the clustering? Why?



# Clustering alternate variables
There might also be an association between the species and the sepal length and width. 
Cluster using those variables instead.


```{r}
set.seed(55)
cluster.iris <- kmeans(iris[, 1:2], 3, iter.max = 10, nstart = 10)
cluster.iris
```


```{r}
table(cluster.iris$cluster, iris$Species)
```

```{r}
cluster.iris$cluster <- as.factor(cluster.iris$cluster)
ggplot(iris, aes(Sepal.Length, Sepal.Width, color=cluster.iris$cluster, shape=Species)) + 
  geom_point() + 
  ggtitle("iris cluster - sepal vs species")
```

  * What do you think influenced the accuracy of these clusters?

Of course, we could try to cluster using all the data. Maybe the more information we have available, 
the easier it is to distinguish between the clusters and hence identify the species.

```{r}
set.seed(55)
# Use all the data to decide the clusters
cluster.iris <- kmeans(iris[, 1:4], 3, iter.max = 10, nstart = 10)
cluster.iris
```

```{r}
table(cluster.iris$cluster, iris$Species)
```

```{r}
cluster.iris$cluster <- as.factor(cluster.iris$cluster)
# Plot using Sepal length and width as the axes
ggplot(iris, aes(Sepal.Length, Sepal.Width, color=cluster.iris$cluster, shape=Species)) + 
  geom_point() + 
  ggtitle("iris cluster - sepal & petal vs species")
```

  * Has this improved the clustering?

  

# Hierarchical clusters
Another alternative is to group data in multiple clusters, such that each cluster is also the member 
of another cluster. The smallest cluster is a single plot and the largest cluster is the entire data.

This type of clustering can be bottom-up or top-down. Let’s explore the bottom-up approach, which starts 
with the smallest cluster then at each iteration, determining which clusters are nearby and the cost 
of their linkage.


# Iris hierarchy
R uses hclust() for this clustering. The default ‘complete’ method of deciding when to link two 
clusters is to measure the euclidean distances of all points in one cluster to each point in the 
other cluster, then find the maximum distance. A preference is to link clusters that are thus similar 
to each other by having a low maximum distance.

```{r}
clusters <- hclust(dist(iris[, 3:4]), method = 'complete')
clusters
```


These are normally visualised as a dendrogram. Unfortunately ggplot2 isn’t written to work with 
dendrograms, so most people use plot() to generate it.

```{r}
plot(clusters)
```

The ggdendro package does try to use ggplot2, but still isn’t quite always practical. 
It does still have some bonuses though.

```{r}
library(ggdendro)
```

```{r}
dhc <- as.dendrogram(clusters)
# Rectangular lines
ddata <- dendro_data(dhc, type = "rectangle")

p <- ggplot(segment(ddata)) + 
  #geom_point() +
  geom_segment(aes(x = x, y = y, xend = xend, yend = yend)) +
  coord_flip() +                          # swap the axes
  scale_y_reverse(expand = c(0.2, 0)) +   # start the new x axis with the highest on the left
  #theme_dendro() +                        # remove the axis
  ggtitle("iris dendrogram - petal length and width")
p
```


```{r}
ddata
```



# Cutting the tree
A full tree may have more depth than needed, so you can choose to cut the tree at a certain number of clusters.


For the iris data, we know there are three species, so we’ll cut at a cluster height of three. 
The results can then be compared to the target species.

```{r}
## Cut after three
clusterCut <- cutree(clusters, k=3)
## Compare to the species
table(clusterCut, iris$Species)
```

If we plot this against the petal length and width, it can be seen where the clustering doesn’t match the species.

```{r}
ggplot(iris, aes(Petal.Length, Petal.Width, shape = iris$Species)) + 
  geom_point(col = clusterCut) + 
  ggtitle("iris dendrogram - petal vs species")
```

# Alternate linkage methods
There are other ways to decide which clusters need to be linked. Another is average which calculates 
the average distance between each point in the one cluster and each point in the other cluster.

```{r}
clusters <- hclust(dist(iris[, 3:4]), method = 'average')
plot(clusters)
```

  * Does this look more reasonable?
  * Does it match the iris species better?

If we plot this against the petal length and width, it can be seen where the clustering doesn’t match the species.

```{r}
ggplot(iris, aes(Petal.Length, Petal.Width, shape = iris$Species)) + 
  #geom_point(alpha = 0.4, size = 3.5) + 
  geom_point(col = clusterCut) + 
  ggtitle("iris dendrogram [average] - petal vs species")
```

Try both the linkage methods for clusters based on the sepal length and width.

  * Is the clustering better than k-means?
  * How can you explain the different results between k-means and hierarchical clustering?

```{r}
clusters <- hclust(dist(iris[, 3:4]), method = 'average')
clusters
```