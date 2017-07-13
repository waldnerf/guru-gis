---
ID: 1028
post_title: Italic label in a plot using facet_wrap
author: White-Guru
post_excerpt: ""
layout: post
permalink: >
  http://www.guru-gis.net/italic-label-in-a-plot-using-facet_wrap/
published: true
post_date: 2017-07-13 22:36:00
---
Hi!
I just got asked by a journal to italicize some labels in a plot... So here is a small code snippet to assist you when you get asked the same:

<pre lang='rsplus'>
factor1=rep(letters[1:3], each=3)
factor2=rep(1:3,times=3)
x=rep(1,9)
y=1:9
df=cbind.data.frame(factor1,factor2,x,y)

levels(df$factor1)= c("a"=expression(paste("factor_", italic("a"))),
                      "b"=expression(paste("factor_", italic("b"))),
                      "c"=expression(paste("factor_", italic("c"))))

ggplot(df, aes(x=x, y=x))+facet_grid(factor2~factor1, labeller=label_parsed)+geom_point()
</pre>

And the result is:
<a href="http://www.guru-gis.net/wp-content/uploads/2017/07/italic.png" rel="attachment wp-att-1029"><img src="http://www.guru-gis.net/wp-content/uploads/2017/07/italic.png" alt="italic" width="577" height="515" class="alignnone size-full wp-image-1029" /></a>

Thanks to Berengere Husson for the tip!