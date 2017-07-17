---
ID: 1032
post_title: Fast Euclidean Distance Function
author: Guru-Blard
post_excerpt: ""
layout: post
permalink: >
  http://www.guru-gis.net/fast-euclidean-distance-function/
published: true
post_date: 2017-07-17 14:42:42
---
Holà GISpo,

Today, a simple function to (highly) speed up the standard Euclidean distance function in R when you use large matrix (n> 100.000.000).

<pre lang='rsplus'>
euc_dist <- function(m) {mtm <- Matrix::tcrossprod(m); sq <- rowSums(m*m);  sqrt(outer(sq,sq,"+") - 2*mtm)} 

# Example
M <- matrix(rexp(1000000, rate=.1), ncol=1000)

ptm <- proc.time()
d <- dist(M, method = "euclidean")
proc.time() - ptm

#   user  system elapsed 
#   1.08    0.00    1.08 

ptm <- proc.time()
d2 <- euc_dist(M)
d2 <- as.dist(d2)
proc.time() - ptm

#   user  system elapsed 
#  0.424   0.008   0.429 

isTRUE(all.equal(as.matrix(d), as.matrix(d2)))
#TRUE
</pre>