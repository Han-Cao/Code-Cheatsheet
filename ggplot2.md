# ggplot2 

#### Default packages

```R
library(dplyr)
library(ggplot2)
```



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
p + theme(strip.txt = element_blank(), strip.background = element_blank)
```



#### Label multiple density plot

```R
library(directlabels)
p <- ggplot(data) + geom_density(aes(x, color=ID))
#label ID for each density plot at top points
direct.label(p, "top.points")
```

