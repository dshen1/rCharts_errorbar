---
title: rCharts Custom | Market Risk Premium Error Bar
author: TimelyPortfolio
github: {user: timelyportfolio, repo: rCharts_errorbar, branch: "gh-pages"}
framework: bootstrap
mode: selfcontained
highlighter: prettify
hitheme: twitter-bootstrap
url: {lib: ../libraries}
assets:
  css:
  - "http://fonts.googleapis.com/css?family=Raleway:300"
  - "http://fonts.googleapis.com/css?family=Oxygen"
---
  
<style>
iframe{
  height:550px;
  width:1000px;
  margin:auto auto;
}

body{
  font-family: 'Oxygen', sans-serif;
  font-size: 16px;
  line-height: 24px;
}

h1,h2,h3,h4 {
  font-family: 'Raleway', sans-serif;
}

.container { width: 900px; }

h3 {
  background-color: #D4DAEC;
    text-indent: 100px; 
}

h4 {
  text-indent: 100px;
}
</style>
  
<a href="https://github.com/timelyportfolio/rCharts_errorbar"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://s3.amazonaws.com/github/ribbons/forkme_right_darkblue_121621.png" alt="Fork me on GitHub"></a>

# rCharts Error Bar of Equity Risk Premium Survey




One of my favorite surveys from academic research on finance is an international survey on market risk premium run by the [IESE Business School at the University of Navarra](http://www.iese.edu/).  Even though I look at it occasionally every year, I never noticed the error bar plot.

![Error Bar from Paper](figurefrompaper.png)

This looks remarkably similar to a custom error bar d3 plot that I implemented for [rCharts](http://rcharts.io).  I guess there are a few of these in the wild.  See [Long Winding Road Marked with Error Bars and Tweets](http://timelyportfolio.blogspot.com/2013/09/long-winding-road-marked-with-error.html) for another example.  I just could not resist making this interactive.

### Data
All data came from this [fine article](http://ssrn.com/abstract=91416), so attribution and credit should be entirely directed there.
<address style="font-size:70%;"><strong>Market Risk Premium and Risk Free Rate Used for 51 Countries in 2013</strong>    
A Survey with 6,237 Answers<p class="muted">Fernandez, Pablo and Aguirreamalloa, Javier and Linares, Pablo<br>June 26, 2013<br>Available at SSRN: http://ssrn.com/abstract=91416</p>
</address>

I did a little copy/paste magic from pdf into Excel and then saved it as a .csv file.  With R, we will read it with the following line of code.


```r
# data source: http://ssrn.com/abstract=91416
mrp <- read.csv("ssrn-id914160.csv",stringsAsFactors=F)
```



### Interactive Error Bar with d3 and rCharts

I never thought I would say this, but the d3/rCharts piece is actually the easiest.  Just specify a couple of parameters, and we have an interactive error bar plot.


```r
## using very experimental version of rCharts
## require(devtools)
## install_github("rCharts", "timelyportfolio", ref = "test-speedimprove")
require(rCharts)

#set working directory to a local and change setLib and templates$script
#if going to github gh-pages repo not desired
#path = "http://timelyportfolio.github.io/rCharts_errorbar"
path = ".."
ePlot <- rCharts$new()
ePlot$setLib(path)

ePlot$templates$script = paste0(path,"/layouts/chart.html")
#not the way Ramnath intended but we'll hack away
ePlot$params =  list(
  data = subset(mrp,variable=="mrp"),
  height = 500,
  width = 1000,
  margin = list(top = 10, bottom = 10, right = 50, left = 100),
  x = "Country",
  y = "mean",
  radius = 2,
  sort = list( var = "mean" ),
  whiskers = "#!function(d){return [d.mean - 1.96 * d.sd, d.mean + 1.96 * d.sd]}!#",
  tooltipLabels = c("Country","mean","sd") 
)
ePlot
```

<iframe src=assets/fig/unnamed-chunk-3.html seamless></iframe>



```r
#example of facetting
ePlotFacet <- rCharts$new()
ePlotFacet$setLib(path)
ePlotFacet$templates$script = paste0(path,"/layouts/chart.html")
ePlotFacet$params =   list(
  data = mrp,
  height = 500,
  width = 1000,
  margin = list(top = 10, bottom = 10, right = 50, left = 100),
  x = "Country",
  y = "mean",
  color = "Country",
  radius = 4,
  sort = list( var = "mean" ),
  whiskers = "#!function(d){return [d.mean - 1.96 * d.sd, d.mean + 1.96 * d.sd]}!#",
  tooltipLabels = c("Country","variable","mean","sd"),
  facet = list(y = "variable")  #add y for facet grid
)
ePlotFacet
```

<iframe src=assets/fig/unnamed-chunk-4.html seamless></iframe>


### Thanks
As you can hopefully tell, I depended heavily on lots of folks to write this little post.  Thanks to:    
1. [Ramnath Vaidyanathan](http://github.com/ramnathv) - [@ramnath_vaidya](https://twitter.com/ramnath_vaidya)    
2. [Carson Sievert](http://cpsievert.github.io/) - [@cpsievert](https://twitter.com/cpsievert)    
2. [Iain Dillingham](http://dillingham.me.uk/)    
4. [missing link](http://fillintheblank.com)    
5. [Mike Bostock](http://bost.ocks.org/mike/)    
6. Everybody else that has contributed R and d3 examples online. I probably have looked at them.
