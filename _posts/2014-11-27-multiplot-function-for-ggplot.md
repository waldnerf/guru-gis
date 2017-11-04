---
ID: 653
post_title: >
  Multiplot function for ggplot (with
  title)
author: Guru-Blard
post_excerpt: ""
layout: post
permalink: >
  http://www.guru-gis.net/multiplot-function-for-ggplot/
published: true
post_date: 2014-11-27 14:27:23
---
Hi GISouille,

Here is a way to get a multiplot with ggplot.

<pre lang="rsplus">
library(ggplot2)

for (i in c(1:(length(iris)-1))){
  nam<-paste0("p", i)
  x.label<-colnames(iris)[i]
  assign(nam, ggplot(iris, aes_string(x.label, fill = "Species")) + 
              geom_density(alpha = 0.3) + 
              xlab(x.label))
}

multiplot(p1, p2, p3, p4, cols=2, title="Multiplot function")
</pre>

<center>
<a href="http://www.guru-gis.net/wp-content/uploads/2014/11/multiplotTitle.png" rel="attachment wp-att-1038"><img src="http://www.guru-gis.net/wp-content/uploads/2014/11/multiplotTitle.png" alt="multiplotTitle" width="785" height="549" class="alignnone size-full wp-image-1038" /></a>
</center>

The function multiplot (adapted from <a href="http://www.cookbook-r.com/Graphs/Multiple_graphs_on_one_page_(ggplot2)/">source</a>):

<pre lang="rsplus">
# Multiple plot function
#
# ggplot objects can be passed in ..., or to plotlist (as a list of ggplot objects)
# - cols:   Number of columns in layout
# - layout: A matrix specifying the layout. If present, 'cols' is ignored.
#
# If the layout is something like matrix(c(1,2,3,3), nrow=2, byrow=TRUE),
# then plot 1 will go in the upper left, 2 will go in the upper right, and
# 3 will go all the way across the bottom.
#
multiplot <- function(..., plotlist=NULL, file, cols=1, layout=NULL, title="") {
  require(grid)
  
  # Make a list from the ... arguments and plotlist
  plots <- c(list(...), plotlist)
  
  numPlots = length(plots)
  
  # If layout is NULL, then use 'cols' to determine layout
  if (is.null(layout)) {
    # Make the panel
    # ncol: Number of columns of plots
    # nrow: Number of rows needed, calculated from # of cols
    layout <- matrix(seq(1, cols * ceiling(numPlots/cols)),
                     ncol = cols, nrow = ceiling(numPlots/cols))
  }
  
  if (nchar(title)>0){
    layout<-rbind(rep(0, ncol(layout)), layout)
  }
  
  if (numPlots==1) {
    print(plots[[1]])
    
  } else {
    # Set up the page
    grid.newpage()
    pushViewport(viewport(layout = grid.layout(nrow(layout), ncol(layout), heights =if(nchar(title)>0){unit(c(0.5, rep(5,nrow(layout)-1)), "null")}else{unit(c(rep(5, nrow(layout))), "null")} )))
    
    # Make each plot, in the correct location
    if (nchar(title)>0){
      grid.text(title, vp = viewport(layout.pos.row = 1, layout.pos.col = 1:ncol(layout)))
    }
    
    for (i in 1:numPlots) {
      # Get the i,j matrix positions of the regions that contain this subplot
      matchidx <- as.data.frame(which(layout == i, arr.ind = TRUE))
      
      print(plots[[i]], vp = viewport(layout.pos.row = matchidx$row,
                                      layout.pos.col = matchidx$col))
    }
  }
}
</pre>