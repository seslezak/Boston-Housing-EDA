# Boston Housing Data Project EDA
## Steven Slezak
### 16 Feb 2018


### Introduction

The Center for Machine Learning and Intelligent Systems at the University of California, Irvine, maintains a web site containing over 400 data sets as a service to the machine learning community.  This site, [the UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/index.php), consists of data bases, domain theories, and data generators that are open source to any users.  This problem statement utilizes the *Housing* [data set found there.](https://archive.ics.uci.edu/ml/machine-learning-databases/housing/)

This problem statement includes the following:

1.  Domain:  Urban Demographics
2.  Problem Statement:  Data Conditioning and Exploratory Data Analysis
3.  Description of Data Set:  506 x 14, Including a Single Target, and Apples-to-Oranges Data
4.  Exploratory Data Analysis:  First Impressions
5.  Proposed Solution:  Multiple Linear Regression with Transformations
6.  Benchmark Model:  Simple Linear Regression
7.  Performance Metrics:  Accuracy
8.  Citations
9.  Jupyter Notebook with Associated Code

As will be shown, there a fundamental problems with the *Housing* data set as it exists.  Other researchers have made efforts to improve it.  This EDA describes some additional measures that might be taken to improve the usefulness of the original data set.

### Domain:  Urban Democgraphics and Various Studies

The *Housing* data set consists of demographic, socioeconomic, and housing statistics collected from hundreds of neighborhoods throughout the Boston metropolitan region in the 1970s.  Known formally as the *Boston Housing Data*, the data describe a variety of features researchers at Harvard University and the University of Michigan believed to be indicators of housing values in the metropolitan region (see citation 2 below). 

Their study was designed to determine the "methodological problems associated with the use of housing market data to measure the willingness to pay for clean air."  So the collection of this data was incidental to the purpose it is generally employed for, which is to predict housing values in these Boston neighborhoods.  

Sources for the data include the 1970 United States Census, the Metropolitan Area Planning Commission (1972), the Massachusetts Taxpayers Foundation (1970), the Federal Bureau of Investigation (1970), the Massachusetts Department of Education (1971 - 1972), the 1970 United States Census Tract Maps, the MIT Boston Project, the Transportation adn Air Shed Simulation (TASSIM), Vogt, Ivers, and Associates, and other sources.

The data do not appear to be primary sources for this study.  They seem to be secondary sources collected for use in other studies, This may help explain some of the problems encountered in this data set.

Subsequent studies (citations 3 and 4 below) corrected data errors in the original data, and added features based on georeferencing.  One study even corrected the target feature in the data set, the median value of homes in the neighborhood.

### Problem Statement:  Data Conditioning and Exploratory Data Analysis

The purpose of this study is to prepare the data for a full analysis later.  The current goal is to condition the data in preparation for analysis and model building, and to make clear the reasoning behind the decisions made in the treatment and preparation of the data.

The conditioning process consists primarily of determining an appropriate sample size for the data, one that is sufficient to describe the characteristics of the data set as a whole.  Besides pulling samples of the data, it will be necessary to convert samples into z-scores, identify correlations between the features, prepare distribution plots, and attempt to standardize the data.  An effort will also be made to identify any outliers and describe a method to manage them.

At this stage, there will be no effort to model the data or develop a predictive methodology though some approaches will be suggested in subsequent sections.

### Description of Data Set:  506 x 14, Including a Single Target, and Apples-to-Oranges Data

The *Housing* data set consists of 14 features representing 506 neighborhoods in the Boston metropolitan area.  All columns are discrete and no data are missing from the table.  All data are numeric, but wo features -- *Charles* and *Radial* -- integers.  *Charles* is a dummy variable used to indicate whether a property sits on the Charles River or not (1 = "Yes") while *Radial* is an index of accessibility to radial highways.  In addition, a complete column from the original data set, *PART*, was excluded from the present *Housing* set.

Certain of the features represent proportions, and not actual counts.  They are:  *Crime*, *LrgLots*, *IndAcr*, *OwnerOcc*, *EdRat*, and *LowStatus*. The column representing the African-American population, *Min* is a formula, `1000 * (B - 0.63)^2` in which *B* is the proportion of African-Americans "by town."  The value for *B* is not given.

According to the original study (citation 2), the rows of data represent individual neighborhoods in the Boston metropolitan region.  But some of the data are expressed "by town."  These include *Crime*, *IndAcr*, and *Min*.  

In processing this data, we are essentially taking averages of averages or medians.  This is basically very confusing, especially when it comes to averages of proportions or percentages.  Given the data available in *Housing* it is not possible to create a true average, so this problem is baked in to the data set.

Since each row represents data from a specific neighborhood, the values associated with that neighborhood are averages.  So when we develop desriptive statistics of the data, we are making averages of averages, standard deviations of averages, and so on.  This complicates interpretation of results, somewhat.

And in making predictions, we would be predicting the average median value of properties in that neighborhood.  

To further confuse matters, the *EdRat* is calculated by school district, which we cannot assume coincides with the boundaries used for identifying neighborhoods and towns. The original study's authors comment on another weakness of this variable, admitting that "the relation of the pupil-teacher ratio to school quality is not entirely clear."

Comparing neighborhoods to towns to school districts creates an "apples-to-oranges" situation which does not seem to be rectified by the data set or the original study.  Perhaps this is why corrected data sets were issued later, but for our purposes it is important to keep this problem in mind when comparing one factor to another.

Note the data set did not come with column names, so they were created for the present analysis.  Column names have been changed from the original study to make them more intelligible.  The original and modified column names are given in the table below.

The data set is small, consisting of 7084 data points occupying 54.7 kB of memory.  There are no missing values, and no duplicate rows.

The description of the data from the original study (citation 2 below) is as follows:

| **Variable** | **Variable Type** | **Definition** | **EDA ID** | **Original ID** |
| :---: | :---: | :---: | :---: | :---: |
| Median Value | Dependent Variable | Value of Owner Occupied Homes (x $1000) | MedVal | MV |
| Rooms | Structural | Average Number of Rooms per Unit | Rms | RM |
| Age | Structural | Proportion of Units Built Before 1940 | OwnOcc | AGE |
| Black | Neighborhood | African-American Proportion of Population | Min | B |
| Lower Status | Neighborhood | Lower Status Proportion of Population | LowStatus | LSTAT |
| Crime | Neighborhood | "Crime Rate by Town" | Crime | CRIM |
| Zone | Neighborhood | Residential Lots Larger than 25,000 Square Feet | LrgLots | ZN |
| Industrial | Neighborhood | "Proportion Non-Retail Business Acres per Town" | IndAcr | INDUS |
| Tax | Neighborhood | Property Tax Rate ($ per $10,000) | PropTax | TAX |
| PT Ratio | Neighborhood | "Pupil-Teacher Ratio by Town School District" | EdRat | PTRATIO |
| Charles | Neighborhood | Dummy Variable for Charles River Proximity | River | CHAS |
| Distance | Accessibility | Distances to Regional Employment Centers | DistWork | DIS |
| Radial | Accessibility | Index of Accessibility to Radial Highways | HiwayAcc | RAD |
| NOX | Air Pollution | Nitrous Oxide, pphm | NOX | NOX |
| Particulates | Air Pollution | Particulate Concentrations, mg/hm^3 | Absent | PART |

### Exploratory Data Analysis:  First Impressions

The Exploratory Data Analysis (EDA) performed on the *Housing* data set employed a variety of statistical analysis and visualization methods to gain insight into the data and attempt to understand the story the data are telling.  The results are decidedly mixed, due to problems with the data mentioned above.  It is not a stretch to say the data are fundamentally flawed and probably would not be reliable for predictive purposes.  Some researchers have in fact corrected the original data set but this study does not examine their efforts or results.

Regardless, the *Housing* data are useful for EDA purposes and are a good training set.

A quick review of density distributions of the feature set show none are true normal distributions.  *River* is a binary feature, and *IndAcr* and *PropTax* are bimodal distributions.  Certain features, such as *Rms* and *MedVal* look slightly normal to the eye, but this does not hold up under analysis.  The best that can be said is certain feature are partially normal, at least around their central tendencies.  

This degree of skew can be seen the in quantile plots found in the associated Jupyter notebook.  The red line in each chart indicates the theoretical shape of the data were they to follow a true normal curve.  The most noticeable characteristic for each is the diversion between the plotted data and the theoretical values occuring in the tails of the actual distributions.  This captures the scope of the outlier problem very well.

The boxplots (plotted against the target, *MedVal*) also show the lack of normality in the distributions, though it appears some features are close to normal over specific quintiles.  So this confirms information we read from the quintile plots.  But that is not enough to say the feature has a normal distribution.

The density charts, boxplots, and quantile plots all demonstrate a great deal of skew exists in the distributions, too much to make them truly normal.  Most of this is right-skew, meaning the mean of the distribution is greater than the median (`median < mean`) and mean is to the right of median.  A great many outliers occupy the distributions' further reaches.

For more discussion of the boxplots, please refer to the *Data Conditioning* section following.

We can see the exact degree of the problem in the tabular analysis of the statistical moments of the various features.  In this table, we have removed the *River* and *HiwayAcc* features, the first because it is a binary feature (values of 0 and 1 only) and the other because it is an index.  Neither lends itself to moment analysis.  Immediately, we notice the disparity between the mean and median values for each feature, and the values for skew.  So our numerical analysis supports our visual analysis.  The *Housing* data are not normally distributed.

In the following sub-sections, we will discuss how sampling was conducted and provide an analysis of the samples drawn.  This is follwed by a discussion of the correlations found in the data.  Then, we will touch upon data conditioning and efforts to normalize the data, to see if this might render the data set more useful.  We will end with a description of the outlier problem and how it might be handled.

#### a)  Sampling Method

A random sampling method was applied using the *R* `sample()` function without replacement.  The sample sizes used were n = 5, n = 30, n = 60, n = 80, and n = 100.  A sample size calculator (citation 7) was used to find an appropriate solution for a data set of 500 rows.  It suggested n = 60, at 90% confidence Level and 10% margin of error.  For 95% confidence level and 10% margin of error, the site suggested a sample of n = 80.

Samples of n = 5, n = 30, and n = 60 were taken.  They did not match the density distributions characteristic of the full data set so these results were discared.  A sample of n = 80 was good, but it was found a sample of n = 100 was better.  So the sample size considered most representative for this study was n = 100.

Comparisons of the density distributions and the data tables can be found in the associated Jupyter notebooks.  The similarity between the original data set and the sample n = 80 is good, but the sample n = 100 is much improved.

#### b)  Sample Analysis

For this section, a sample of n = 5 was pulled from the original data.  This sample is far too small to be rerpresentative, but the dramatic difference between the statistical characteristics of this sample and those of the original data set will serve to illustrate some exercises in comparing and standardizing data.

A quick comparison of the visualizations of the small sample and the original data set shows they are very dissimilar.  This is confirmed by a comparison of the tabular summary data -- mean, standard deviation, median, skew, and kurtosis -- of each, which displays similar dissimilarity.  The tabular data also show that neither the original data set nor the sample exhibit the characteristics of a normal distribution, asas discussed in a previous section.

The next step in this part of the analysis was to convert the sample data into z-scores, then create heatmaps and barplots of the sample and sample z-scores.  This illustrates the standardizing properties of data transformation using z-scores well.

The sample data display no relationhip to a normal distribution.  But, when the data are converted into z-scores and visualized with a histogram, a fairly normal distribution emerges.  This impression is confirmed when we run the sample z-scores in a normal quantile plot and superimpose the line the data should follow were they distributed in a theoretical normal curve.  The sample z-scores align pretty well with the normal distribution, at least better than we saw earlier in the quantile plots run on the original data set.

Since each row in the *Housing* data set represents aggregate data for a particular neighborhood in the Boston metropolitan region, the five samples we drew are particular neighborhoods.  Since we took such a small random sample, the likelihood is very low it would be representative of the complete data set.  So the best we can say is that the five samples represent their particular neighborhoods in the aggregate.

Comparing to the means for the complete data set, we can say the following about the sample:

| **Original Variable** | **Compared to Sample Variable** |
| :---: | :---: |
| Crime | Higher |
| Large Lots | Greater |
| Industrial Acreage | About the Same |
| NOX Levels | About the Same |
| Average Rooms per House | About the Same |
| Owner Occupancy Rate | About the Same |
| Distance to Work | About the Same |
| Property Tax | Higher |
| Pupil Teacher Ratio | About the Same |
| Minority Population | Lower |
| "Low Status" Population | Greater |
| Median House Value | Higher |

For reasons mentioned earlier, flaws in the way the data are presented make it difficult to compare values feature to feature.  The units are dissimilar, and the meaning of certain features, such as the measure of minority population, is unclear.  As can be seen in the density plots, the scales are very different.  And since subsequent researchers had to correct the data found in the original data set, including the median house value target, it is difficult to treat this data set as reliable.

#### c)  Correlation Analysis

Scatterplots of the data indicate certain linear relationships might exist.  The scatterplots suggest a loose grouping that could be interpreted as linear, so a correlation study was run.  The correlation table/heatmap generated for the study shows high levels of positive and negative correlations exist between many of the variables.  The one feature that has nearly no correlation to the others is *River*, which is a binary feature indicating proximity to the Charles River.

To determine if any redundancy exists between features a variance inflation factor (VIF) test for multicollinearity was employed.  The VIF test runs a regression of each feature against all other predictor features.   According to research (citation 8), for a pair of variables with a strong linear relationship, correlation would be high and the VIF for that pair of variables would be large.  If a variable has a strong linear relationship with at least one other variables, the correlation coefficient would be close to 1, and VIF for that variable would be large. 

In this study, we utilized two *R* functions for this purpose.  They are `vif()` and `vifcor()`.  For `vif()`, a value greater than 10 would indicate some kind of collinearity problem with the variable.  The `vif()` test on the *Housing* data yields values less than 10.  The data were also run through the `vifcor()` test, which uses a `th = ` switch to set a minimum for a correlation value between variables.  In this test, `th = 0.7` was used.  The `vifcor()` test indicated that the *NOX*, *DistWork*, *LowStatus*, and *IndAcr* variables exhibit collinearity.  

One interesting result of these two test is that the minimum correlations for both are *Min ~ Rms* at 0.13 but the max correlations are *DistWork ~ NOX* at -0.77 for the `vif()` test and *MedVal ~ Rms* at 0.70 for the `vifcor()` test.  So removing the four features suspected of collinearity makes a big change on the maximum correlation values, from negative to positive.

That these four features may be collinear is something to consider when building a linear model for predictive purposes.

The correlation table/heatmap and pairplots can be found in the associated Jupyter notebook.

#### d)  Data Conditioning

In this section, we want to compare the original data set to two transformations performed on it.  One is a standarization using Z-scores.  The other is a log conversion, followed by standardization.  We want to demonstrate that scaling does not impact the distribution of data.  The only thing changed by scaling is literally the scale of the plots themselves.  Specifically, we are speaking of the x-axis values.

Comparing histograms of the original data to the scaled data demonstrates the distributions do not change due to scaling.  Means, medians, and standard deviations are the same in both examples.  What changes is simply the scale.  We should note that, since we are using histograms, the scaling may cause some slight changes in count between bins, but this in no way impacts the aggregate distribution.

Another way to consider this is to examine boxplots of the original data compared to the standardized set, again using Z-scores as the standardization method.  

In our boxplots, the original data are treated in two different manners.  In the first, data are divided into two sections then plotted, so we can see the subsets of each feature.  In the second method, data are divided by quintiles then plotted.  This is done for both the original and standardized data sets.

The boxplots clearly show the skew in the data distribution for each feature at the quintile level.  Outliers are also identified in the boxplots, so the direction of the skew is not difficult to see.

To interpret the boxplots, keep in mind:

1.  the left and right boundaries of the box represent the ends of the 1st and 3rd quartiles, respectively.
2.  the line through the box is the median.
3.  the length of the box is the interquartile range (IQR).
4.  the line in the middle of the box is the median, or the 2nd quartile.
5.  the left whisker is the minimum value or the 1st quartile less 1.5 times IQR, whichever is less.
6.  the right whisker denotes the maximum value or the 3rd quartile plus 1.5 times IQR, whichever is greater.
7.  any outliers beyond these boundaries can be used in the Tukey method to determine the percentage of total data that are outliers.

Boxplots are used to present the original data and for the original data scaled using Z-scores.  Comparing the two sets of boxplots, it is easy to see that scaling has no impact on the structure of the distribution.  The only changes are to the values along the x-axis.  Now they are standardized and comparable.

Similarly, boxplots are used to present the log of the original data and for the log data standardized using Z-scores.  The distributions are clearly very different from the first set of boxplots, as we would expect.  But there is no difference between the distributions of the log data and the log data scaled.  Given the nature of the data, a log transformation might not be the most useful or appropriate method to take.

Relgardless, an important advantage to using boxplots in this analysis is we can literally count the outliers for each distribution.  In making the comparison it is evident there is no change between the distributions on the quintile level, so we can expect none in the aggregate.

The fact we can count outliers in the boxplots is useful.  Boxplots, also known as box and whisker diagrams, provide detailed information about data distributions, moreso than histograms.  With further analysis, we can analyze information in outliers to assess how prevalent they are in the data set and develop an approach to dealing with them.

#### e)  Outlier Analysis

The *Housing* data set is characterized by its many outliers, so it would be useful to take a look at them.  To do this, we will use the Tukey Method, which we are already familiar with from the boxplot charts used in this study.  This time, we will use the Tukey Method to identify the outliers and see what proportion of the data they constitute. 

The table bedlow summarizes the outlier information.  There are 303 total outliers, or 4.3% of the 7084 data points in the *Housing* data set.  This analysis was made using a function written in *R* that utilizes the Tukey Method for identifying outliers.  The *R* command `boxplot.stats()` uses the Tukey Method of identify outliers beyond the range of 1.5 x IQR.

| **Variable Name** | **Number of Outliers** | **Percent of Outliers** |
| :---: | :---: | :---: |
| Crime | 66 | 15% |
| Large Lots | 68 | 16% |
| Industrial Acreage | 0 | 0% |
| NOX Levels | 0 | 0% |
| Average Rooms per House | 30 | 6% |
| Owner Occupancy Rate | 0 | 0% |
| Distance to Work | 5 | 1% |
| Property Tax | 0 | 0% |
| Pupil Teacher Ratio | 15 | 3% |
| Minority Population | 76 | 18% |
| "Low Status" Population | 6 | 1% |
| Median House Value | 37 | 8% |
| **Total Outliers** | **303 of 7084 Data Points** | **4%** |

Since the proportion of outliers is relatively small, it might make sense to remove them from the data set before analysis and modeling.  We could see if the remaining data are in fact normally distributed, as we assume.  One problem with this, however, is the data set itself is pretty small.  Another concern is other problems with the data set discussed in this study would not be solved by dropping outliers.  Finally, we should investigate if the outliers exist because the data set is basically taking averages of averages (or worse, averages of medians), which could distort the distribution we are working with.

An alternative approach would be to abandon the assumption the data are normally distributed and find a more appropriate distribution curve that would incorporate the outliers.  That would effectively remove those data points as "outliers" without losing the information they carry.  
        
### Proposed Solution:  Multiple Linear Regression with Transformations

If we were to develop a predictive model using the *Housing* data set, a good solution would be multiple linear regression.  We could perform some feature engineering to see if creating new features from the exsiting ones would help improve a model's predictive power. A multiple linear regression using all the features would allow us to identify which are most significant, and should be retained, and which can be dropped.  We could then remove the less useful features and focus on those remaining.

It would also be useful to create some interaction terms and see what impact they might have on the coefficients.  

We could also make use of the transformations we inflicted on the data to see how they might contribute to improved predictiveness in a model.  One idea would be to perform a polynomial transformation during the regression analysis.  Another might be to work with the results from the log transformation we performed as part of the EDA.

Of course, some of the problems inherent in the data set could limit the validity of any predictions a model might yield, so those issues need to be kept in mind when interpreting any results.  Garbage in, garbage out.

### Benchmark Model:  Simple Linear Regression

Were we to develop a predictive model using the *Housing* data set, a good naive model would be simple linear regression.  Some of the data plots we created indicate a linear relationship might exist between variables and the target, *MedVal*, so that would be a good place to begin.

### Performance Metrics:  Accuracy

Using the simple linear regression model, we could assess accuracy using the multiple R-square and the F-statistsic.  Since it is a simple linear regression model, we would have to run it once for each feature relative to the target, *MedVal*.  Using a multiple linear regression model, we could assess accuracy using the residual standard error and the R-squared.

### Citations

1. Lichman, M. (2013). UCI Machine Learning Repository [http://archive.ics.uci.edu/ml]. Irvine, CA: University of California, School of Information and Computer Science.

2. Harrison, D. and D. L. Rubinfeld (1978). Hedonic prices and the demand for clean air. Journal of Environmental Economics and Management, 5, 81-102.

3. Gilley, O.W., and R. Kelley Pace (1996). On the Harrison and Rubinfeld Data. Journal of Environmental Economics and Management, 31, 403-405. [Provided corrections and examined censoring.]

4. Pace, R. Kelley, and O.W. Gilley (1997). Using the Spatial Configuration of the Data to Improve Estimation. Journal of the Real Estate Finance and Economics, 14, 333-340. [Added georeferencing and spatial estimation.]

5. James, Gareth, Daniela Witten, Trevor Hastie, and Robert Tibshirani. An Introduction to Statistical Learning: with Applications in R. Springer: 2017.

6. R-Studio. Package: *mlbench*, *Boston Housing* data.

7. Survey Monkey Sample Size Calculator [https://www.surveymonkey.com/mp/sample-size-calculator/]

8. Naimi, B., Hamm, N.A.S., Groen, T.A., Skidmore, A.K., and Toxopeus, A.G. 2014. Where is positional uncertainty a problem for species distribution modelling?, Ecography 37 (2): 191-203.

### Python Notebook with Associated *R* Code

The Python Notebook with associated *R* code can be found in the directory attached to this GitHub repository.

