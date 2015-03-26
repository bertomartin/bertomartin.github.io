---
layout: post
title:  "Healthcare Expenditures: Critique"
date:   2015-03-25
image: /images/health_systems_comparison_OECD_2008.png
blurb: "Too much bling in this chart"
---
This graph shows the amount of money select countries spend on healthcare

[from Wikipedia](http://en.wikipedia.org/wiki/Health_system)

![Comparison of expenditures chart](/images/health_systems_comparison_OECD_2008.png){: .center-image }


* Ink to information ratio very large here (lots of ink for saying very little)
* Missing clear axes
* overall, just looks gaudy

## Improved Charts

### Scatter Plot
![Empire viewership chart](/images/healthcare1.png){: .center-image }

### Dropping a country
![Empire viewership chart](/images/healthcare2.png){: .center-image }

{% highlight text %}

library(RCurl)
library(XML)

theurl <- "http://en.wikipedia.org/wiki/Health_system"
webpage <- getURL(theurl)
webpage <- readLines(tc <- textConnection(webpage)); close(tc)

pagetree <- htmlTreeParse(webpage, error=function(...){}, useInternalNodes = TRUE)

# Extract table header and contents
tablehead <- xpathSApply(pagetree, "//*/table[@class='wikitable sortable']/tr/th", xmlValue)
results <- xpathSApply(pagetree, "//*/table[@class='wikitable sortable']/tr/td", xmlValue)

# Convert character vector to dataframe
content <- as.data.frame(matrix(results, ncol = 10, byrow = TRUE), stringsAsFactors=FALSE)

# Clean up the results
tblhead = c("Country", "Lifeexpectancy", "Infant mortality rate", "Preventable deaths per 100,000 people in 2007","Physicians per 1000 people", "Nurses per 1000 people", "Percapitaexpenditure", "HealthcarepercentGDP", "% of government revenue spent on health", "% of health costs paid by government")
names(content) <- tblhead

content2 = content[, c("Country", "Lifeexpectancy", "Percapitaexpenditure", "HealthcarepercentGDP")]
content2[, 3] = as.numeric(gsub(',', '', content2[,"Percapitaexpenditure"]))
content2[, c(2,4)] <- sapply(content2[, c(2,4)], as.numeric)



ggplot(content2, aes(x=Percapitaexpenditure, y=Lifeexpectancy, fill=HealthcarepercentGDP)) +
  geom_point(shape=21, size=5.5) +
  scale_fill_gradient(low="white", high="black", guide=guide_legend()) +
  geom_text(aes(label=Country), size=4, vjust=-1, hjust=0.5) +
  xlab("Per capita expenditure on health (USD PPP)") + ylab("Life Expectancy") +
  ggtitle("Health Care Spending Vs Life Expectancy") +
  theme(axis.line = element_line(colour="black")) +
  theme(plot.title=element_text(size=16, family="Verdana", face="bold.italic", lineheight=1.2, color="blue"))


# remove some labels
content_copy <- content2
content_copy$Country1 <- content2$Country
idx <- content_copy$Country1 %in% c("Japan", "Australia", "Canada", "France", "Italy", "UK", "Germany", "Norway", "USA")
content_copy$Country1[!idx] <- NA

ggplot(content_copy, aes(x=Percapitaexpenditure, y=Lifeexpectancy, fill=HealthcarepercentGDP)) +
  geom_point(shape=21, size=5.5) +
  scale_fill_gradient(low="white", high="black", guide=guide_legend()) +
  geom_text(aes(label=Country1), size=4, vjust=-1, hjust=0.5) +
  xlab("Per capita expenditure on health (USD PPP)") + ylab("Life Expectancy") +
  ggtitle("Health Care Spending Vs Life Expectancy") +
  theme(axis.line = element_line(colour="black")) +
  theme(plot.title=element_text(size=16, family="Verdana", face="bold.italic", lineheight=1.2, color="blue"))

{% endhighlight %}

