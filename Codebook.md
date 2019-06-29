# Code Book for US Craft Beer Analysis

This repo stores an analysis of US Craft Beer data. The final analysis is delivered in a 5 minute video whose audience would be someone at  the C-Suite level. In particular, we addressed the analysis to Carlos Brito, the CEO of InBev / Anheuser Busch. The primary data source is two csv files, one on individual craft beers and another containing geographic information on craft breweries. The data was provided to us by Dr. Sadler.

William Arnost's video can be found here: https://youtu.be/CmV8fE-PLQk   
Meisam A. Mansor's video can be found here: https://youtu.be/xMe7pj2_Nls    
Github Repository: https://github.com/warnost/CaseStudy01

### Main data files:
beer.csv  
breweries.csv  

### Beers.csv:  
Name: Name of the beer.  
Beer_ID: Unique identifier of the beer.  
ABV: Alcohol by volume of the beer. Units are in decimal, read as percent  
IBU: International Bitterness Units of the beer. Units are IBU  
Brewery_ID: Brewery id associated with the beer.  
Style: Style of the beer.  
Ounces: Ounces of beer.  

### Breweries.csv:  
Brew_ID: Unique identifier of the brewery.  
Name: Name of the brewery.  
City: City where the brewery is located.  
State: U.S. State where the brewery is located.  

### Both datasets are in tidy format, we did two main operations
* Merge the two datasets
* aggregate by state

Here is the code we use to combine the two data sets. We use brewery ID as the join column. We also rename the "Name" column in each data set. "Name" from beers.csv is called "Beer", and "Name" from breweries.csv is "Brewery". The combined dataset is called "combined".

```
combined <- beers %>% left_join(breweries, by = c("Brewery_id" = "Brew_ID")) %>% rename(Beer = "Name.x", Brewery = "Name.y")
```

We also aggregated the "combnined" data by state. We group the data by state, then for each state calculate unique brewery count, median ABV, median IBU, Max ABV, and Max IBU.

```
byState <- combined %>% select(State, Brewery_id, ABV, IBU) %>% 
  group_by(State) %>% 
  summarise(Brewery_Count = n_distinct(Brewery_id), 
    Median_ABV = median(ABV,na.rm=TRUE), 
    Median_IBU = median(IBU,na.rm=TRUE), 
    Max_ABV = max(ABV,na.rm=TRUE), 
    Max_IBU = max(IBU,na.rm=TRUE))
```

We also calculated summary statistics for ABV and IBU using the following code. The first two codes do histograms for ABV and IBU respectively. the second two codes create summary statistic tables for ABV and IBU respectively.
```
ggplot(beers, aes(x=ABV)) + geom_histogram() + 
  labs(title = "Distribution of Alcohol by Volume (ABV)", 
    x="Alcohol by Volume", y="Number of Beers") +
  theme(legend.position = "none",
    plot.title = element_text(hjust = 0.5),
      axis.title.y=element_text(angle=0,vjust=1))
      
ggplot(beers, aes(x=IBU)) + geom_histogram() + 
  labs(title = "Distribution of International Bitterness Unit (IBU)", 
    x="Bitterness (IBU)", y="Number of Beers") +
  theme(legend.position = "none",plot.title = element_text(hjust = 0.5),
    axis.title.y=element_text(angle=0,vjust=1))    
    
kable(as.array(summary(combined$ABV)), digits = 3, col.names = c("Stat","Value")) %>% kable_styling(full_width = FALSE)

kable(as.array(summary(combined$IBU)), digits = 3, col.names = c("Stat","Value")) %>% kable_styling(full_width = FALSE)
```

The complete code file is named WilliamArnost_MeisamMansor_CaseStudy01.Rmd. To run the RMD, clone the repository to your computer, make sure you have the required packages installed, and knit the document. To view without running the code, you can just open the html file.

Required packages:
knitr  
rmdformats  
tidyverse   
kableExtra   
Amelia     
naniar     
maps       
mapproj    
ggthemes   

### Conclusions

Colorado and California have the most craft breweries  
IPA and Pale Ale are the most popular styles  
Craft Beers tend to have alcohol content between 5% and 6.7%  
There is a positive relationship between alcohol content and bitterness in craft beers  
Lee Hill Series Volume 5 is the most alcoholic craft beer (12.8%)  
Bitter Bitch is the most bitter beer (138 IBU) 