# ggplot2 



#### Arrange multiple plots

```R
library(gridExtra)
#set by the number of rows or columns
grid.arrange(..., ncol = 2)
grid.arrange(..., nrow = 2)
grid.arrange(..., ncol=1, nrow=4)
#set by layout matrix
#use NA to assign blank plot
grid.arrange(..., layout_matrix = rbind(c(1,2,NA), c(3,4,5)))
```



#### Arrange multiple plots with shared legend

```R
library(lemon)
grid_arrange_shared_legend(..., position = c("bottom", "right", "top", "left"))
```



