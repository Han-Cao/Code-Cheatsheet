# ggplot2 



### Default packages

---

```R
library(dplyr)
library(ggplot2)
```



### Plot multiple panels

---

#### Arrange multiple plots

```R
library(gridExtra)
#Arrange by the number of rows or columns
grid.arrange(..., ncol = 2)
grid.arrange(..., nrow = 2)
grid.arrange(..., ncol=1, nrow=4)
#Arrange by layout matrix
#use NA to assign blank plot
grid.arrange(..., layout_matrix = rbind(c(1,2,NA), c(3,4,5)))
```

#### Arrange multiple plots on multiple pages of pdf

```R
library(gridExtra)
pdf("test.pdf")
grid.arrange(p1,p2,p3,p4, ncol=2) #page1
grid.arrange(p5,p6, nrow=2, ncol=2) #page2
dev.off()
```

#### Arrange multiple plots with shared legend

```R
library(lemon)
grid_arrange_shared_legend(..., position = c("bottom", "right", "top", "left"))
```

#### Split plot

```R
#vertical split
p + facet_grid(. ~ group)
#horrizonal split
p + facet_grid(group ~ .)
#remove all the labels created by facet_grid
p + theme(strip.text = element_blank(), strip.background = element_blank())
```



### Density plot

---

#### Label multiple density plot

```R
library(directlabels)
p <- ggplot(data) + geom_density(aes(x, color=ID))
#label ID for each density plot at top points
direct.label(p, "top.points")
```

#### Comparable partial density plot

Sometimes we want to plot part of the data for better visualization (i.e. sum of area < 1). For example, considering zero-inflated data, we are interested in the proportion and distribution of those non-zero values. If we plot all values including zero, it could be hard to see the distribution of non-zero value. Here we can calculate the density with all data first, and then manually plot the density plot. The sum of area is the proportion of non-zero value, which is comparable between different groups.

`geom_density_ridges` : plot different group in one panel, but can not set y limits (because y is set as group).

`geom_density` : plot one group per panel, can set y limits.

```R
library(ggridges)

#calculate density
density_df <- data_df %>% group_by(group) %>% 
  group_modify(~ ggplot2:::compute_density(.x$value, NULL, bw = 0.05)) %>%  rename(value = x)

#ggridges
#As kernal density is calculated, we should set cut-off greater than 0 to filter raw 0 data
ggplot(filter(ridge_df_density_diag, value > 0.25)) + 
  geom_density_ridges(aes(transcript, rs7019575, height=density), stat = "identity", size=0.3, scale=1) 

#geom_density
ggplot(filter(ridge_df_density_diag, value > 0.25)) + 
  geom_density(aes(value, density), stat = "identity", fill="grey80", size=0.3) + facet_grid(group ~ .)
```



