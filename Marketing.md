---


---

<pre class=" language-sas"><code class="prism  language-sas"><span class="token comment">/*
	The goal of this project is to determine what could be a factor in the number of purchases, 
	the number of catalog purchases, or the number of web purchases and what factors drive
	in general sales. Analyze the data set to understand the problems with revenue and propose
	a data-driven solution. 
	
	For this reason, I will preform descriptive statistics to organize, describe and summarize
	the data. I will use percentages, averages, variability, simple graphs, charts and tables to
	do each. This will help me to better understand the data by describing and summarizing its
	basic features. The goal for describing the data will be to screen for unusual data, inspect
	the spread and shape of the data, characterize the central tendency, and draw preliminary
	conclusions about the data.
	
	-Is the data error free as possible?
	-What unique features was I able to identify?
	-Are there data values that cluster or show some unusual shape?
	-Does the data include any possbile outliers?
	
	Then, once I have a basic unerstanding of my data, I will use inferential statistics to draw
	conclusions about a population from analysis of a random sample drawn from this population.
	I will investigate the precision and reliability of those inferences.
	
	Notes:
	- JC investigated the effect that income had on purchases and web visits.
	- Found the covariance and correlation
	- Sylvia finished basic data cleansing.
	
	1. Which product categories (fruits, wine, etc.) drove the biggest profits?
	2. Of the top 3 selling categories, were the customers married or single? Kids/teens?
	3. Break up the income amounts into low, medium and high. Compare sales with no discount vs
		sales with discounts, which is higher? Which income bracket purchases the most with
		discounts.*/</span>

<span class="token comment">****EXPLORING AND VALIDATING DATA****;</span>
<span class="token comment">* Lets just quickly view the 1st 10 observations and see what it looks like.  ;</span>
<span class="token keyword">PROC PRINT</span> <span class="token keyword">data</span><span class="token operator">=</span>mark<span class="token punctuation">.</span>marketing <span class="token punctuation">(</span>obs<span class="token operator">=</span><span class="token number">10</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>


<span class="token operator">*</span> There seem to be some categories of marital status that could be combined for this analysis<span class="token punctuation">.</span> 
Combined <span class="token string">"divorced"</span> and <span class="token string">"widow"</span><span class="token punctuation">,</span> <span class="token string">"married"</span> and <span class="token string">"together"</span> and <span class="token string">"2nd cycle"</span> with <span class="token string">"masters"</span> <span class="token punctuation">;</span>

<span class="token keyword">proc sql</span><span class="token punctuation">;</span>
create table mark<span class="token punctuation">.</span>newmarketing as
select <span class="token operator">*</span>
<span class="token punctuation">,</span>	case
		when marital_divorced <span class="token operator">=</span> <span class="token number">0</span> <span class="token keyword">then</span> <span class="token number">0</span>
		when marital_divorced <span class="token operator">=</span> <span class="token number">1</span> <span class="token keyword">then</span> <span class="token number">1</span>
		when marital_widow <span class="token operator">=</span> <span class="token number">0</span> <span class="token keyword">then</span> <span class="token number">0</span>
		when marital_widow <span class="token operator">=</span> <span class="token number">1</span> <span class="token keyword">then</span> <span class="token number">1</span>
		<span class="token keyword">else</span> <span class="token number">0</span> end as Divorced_Widowed
<span class="token punctuation">,</span>	case
		when marital_married <span class="token operator">=</span> <span class="token number">0</span> <span class="token keyword">then</span> <span class="token number">0</span>
		when marital_married <span class="token operator">=</span> <span class="token number">1</span> <span class="token keyword">then</span> <span class="token number">1</span>
		when marital_together <span class="token operator">=</span> <span class="token number">0</span> <span class="token keyword">then</span> <span class="token number">0</span>
		when marital_together <span class="token operator">=</span> <span class="token number">1</span> <span class="token keyword">then</span> <span class="token number">1</span>
		<span class="token keyword">else</span> <span class="token number">0</span> end as Married_Together
<span class="token punctuation">,</span>	case
		when <span class="token string">'education_2n Cycle'</span>n <span class="token operator">=</span> <span class="token number">0</span> <span class="token keyword">then</span> <span class="token number">0</span>
		when <span class="token string">'education_2n Cycle'</span>n <span class="token operator">=</span> <span class="token number">1</span> <span class="token keyword">then</span> <span class="token number">1</span>
		when education_master <span class="token operator">=</span> <span class="token number">0</span> <span class="token keyword">then</span> <span class="token number">0</span>
		when education_master <span class="token operator">=</span> <span class="token number">1</span> <span class="token keyword">then</span> <span class="token number">1</span>
		<span class="token keyword">else</span> <span class="token number">0</span> end as Masters_2nCycle	
<span class="token punctuation">,</span>	case 
		when income between <span class="token number">0</span> and <span class="token number">20000</span> <span class="token keyword">then</span> <span class="token string">'low'</span>
		when income between <span class="token number">20001</span> and <span class="token number">49999</span> <span class="token keyword">then</span> <span class="token string">'medium'</span>
		when income between <span class="token number">50000</span> and <span class="token number">113734</span> <span class="token keyword">then</span> <span class="token string">'high'</span>
	<span class="token keyword">else</span> <span class="token string">'N/A'</span> end as Income_Levels
from mark<span class="token punctuation">.</span>marketing<span class="token punctuation">;</span>
<span class="token keyword">quit</span><span class="token punctuation">;</span>

<span class="token operator">*</span> Questions about the <span class="token keyword">data</span> variables 
<span class="token number">1</span><span class="token punctuation">.</span> Define or guess the parameter and statistic<span class="token punctuation">.</span> The total avg amount spent per customer is <span class="token punctuation">$</span><span class="token number">150</span>
	and avg income of the sample size is <span class="token punctuation">$</span><span class="token number">60</span><span class="token punctuation">,</span><span class="token number">000</span><span class="token punctuation">.</span>
<span class="token number">2</span><span class="token punctuation">.</span> Which variables are continuous or categorical ? Of the variables that I will be investigating<span class="token punctuation">,</span>
	<span class="token string">"Income"</span> is a discrete variable<span class="token punctuation">,</span> <span class="token string">"income_levels"</span><span class="token punctuation">,</span> <span class="token string">"age"</span> and <span class="token string">"education_"</span> are categorical 
	because there is a clear order<span class="token punctuation">.</span>
<span class="token number">3</span><span class="token punctuation">.</span> Of the categorical which are ordinal and nominal? <span class="token string">"income_levels"</span> is an ordinal variable and <span class="token string">"age"</span> and
	<span class="token string">"education_"</span> are nominal variables<span class="token punctuation">.</span>

	<span class="token operator">*</span> The surveyselect procedure selects a random sample from a SAS <span class="token keyword">data</span> set<span class="token punctuation">.</span> 
METHOD<span class="token operator">=</span>srs is simple random sampling without replacement 
SEED<span class="token operator">=</span>specifies the initial seed for random number generation<span class="token punctuation">.</span> <span class="token keyword">if</span> no seed option specified<span class="token punctuation">,</span> SAS
uses the system time as its seed value<span class="token punctuation">.</span> This creates a different random sample every time the
procedure runs<span class="token punctuation">.</span>
SAMPLESIZE<span class="token operator">=</span>indicated the number of rows to be included <span class="token operator">in</span> the sample aka the sample size<span class="token punctuation">.</span> First
look at a random sample size of <span class="token number">12</span> <span class="token keyword">then</span> I look at a simple random sample of <span class="token number">12</span><span class="token punctuation">%</span><span class="token punctuation">.</span>  I believe I 
want to work with a sample size of <span class="token number">12</span><span class="token punctuation">%</span><span class="token punctuation">.</span><span class="token punctuation">;</span>

<span class="token keyword">PROC SURVEYSELECT</span> <span class="token keyword">data</span><span class="token operator">=</span>mark<span class="token punctuation">.</span>newmarketing
	seed<span class="token operator">=</span><span class="token number">31475</span>
	method<span class="token operator">=</span>srs
	sampsize<span class="token operator">=</span><span class="token number">12</span>
	out<span class="token operator">=</span>work<span class="token punctuation">.</span>marketingsample12<span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span> 

<span class="token keyword">proc print</span> <span class="token keyword">data</span><span class="token operator">=</span>work<span class="token punctuation">.</span>marketingsample12<span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>
	 
<span class="token keyword">PROC SURVEYSELECT</span> <span class="token keyword">data</span><span class="token operator">=</span>mark<span class="token punctuation">.</span>newmarketing
	seed<span class="token operator">=</span><span class="token number">13094425</span>
	method<span class="token operator">=</span>srs
	samprate<span class="token operator">=</span><span class="token number">0.05</span>
	out<span class="token operator">=</span>work<span class="token punctuation">.</span>marketing12pc<span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>

<span class="token keyword">proc print</span> <span class="token keyword">data</span><span class="token operator">=</span>work<span class="token punctuation">.</span>marketing12pc<span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>



	<span class="token operator">*</span> But first<span class="token punctuation">,</span> I need to know the scale of measurment for each variable <span class="token operator">in</span> order to determine
the statistical procedures appropriate for use with each variable<span class="token punctuation">.</span> The appropriate statistical
method for my <span class="token keyword">data</span> also depends on the # of variables involved<span class="token punctuation">.</span> Univariate analysis provides
techniques for analyzing and describing a single variable at a time and reveals patterns <span class="token operator">in</span> the
<span class="token keyword">data</span> when we look at the range of values<span class="token punctuation">,</span> measures of dispersion<span class="token punctuation">,</span> the central tendency of the
values<span class="token punctuation">,</span> and frequency distribution<span class="token punctuation">.</span> I can see the avg income and avg age of customers<span class="token punctuation">.</span> With
bivariate analysis I can determine whether the avg income for married or single people is
significantly different <span class="token punctuation">(</span>note that I combined the marital statuses into married<span class="token punctuation">,</span> single or
divorced<span class="token punctuation">)</span><span class="token punctuation">.</span>   <span class="token punctuation">;</span>

ODS TRACE ON<span class="token punctuation">;</span>
<span class="token keyword">PROC UNIVARIATE</span> <span class="token keyword">data</span><span class="token operator">=</span>mark<span class="token punctuation">.</span>newmarketing<span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>
ODS TRACE OFF<span class="token punctuation">;</span>

ods select <span class="token number">Ex</span>tremeValues<span class="token punctuation">;</span>
<span class="token keyword">PROC UNIVARIATE</span> <span class="token keyword">data</span><span class="token operator">=</span>mark<span class="token punctuation">.</span>newmarketing nextrval<span class="token operator">=</span><span class="token number">5</span><span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>

<span class="token keyword">PROC FREQ</span> <span class="token keyword">data</span><span class="token operator">=</span>mark<span class="token punctuation">.</span>newmarketing<span class="token punctuation">;</span>
table income<span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>

<span class="token keyword">PROC UNIVARIATE</span> <span class="token keyword">data</span><span class="token operator">=</span>work<span class="token punctuation">.</span>marketing12pc<span class="token punctuation">;</span>
	var income age <span class="token punctuation">;</span>
	id income<span class="token punctuation">;</span>
	histogram income age <span class="token operator">/</span> normal <span class="token punctuation">(</span>mu<span class="token operator">=</span>est sigma<span class="token operator">=</span>est<span class="token punctuation">)</span><span class="token punctuation">;</span>
	inset skewness kurtosis <span class="token operator">/</span> position<span class="token operator">=</span><span class="token operator">ne</span><span class="token punctuation">;</span>
	probplot income age <span class="token operator">/</span> normal <span class="token punctuation">(</span>mu<span class="token operator">=</span>est sigma<span class="token operator">=</span>est<span class="token punctuation">)</span><span class="token punctuation">;</span>
	inset skewness kurtosis<span class="token punctuation">;</span>
	title <span class="token string">'Descriptive Statistics Using PROC'</span><span class="token punctuation">;</span>
	<span class="token keyword">run</span><span class="token punctuation">;</span>


<span class="token operator">*</span> Now that I have <span class="token number">ex</span>amined our <span class="token number">3</span> major variables <span class="token punctuation">(</span>income<span class="token punctuation">,</span> age<span class="token punctuation">,</span> and martial status<span class="token punctuation">)</span><span class="token punctuation">,</span> I will <span class="token number">ex</span>plore the particular values <span class="token operator">in</span> the <span class="token keyword">data</span><span class="token punctuation">.</span> Before I do any
statistical analysis I will ensure that the <span class="token keyword">data</span> is error free as possible<span class="token punctuation">.</span> I will check for any incomes or ages that are unusually high or low<span class="token punctuation">.</span>
I will <span class="token number">ex</span>plore any outliers or the top <span class="token number">5</span> <span class="token number">ex</span>tremities and consider removing them from my analysis by using the IQR<span class="token punctuation">.</span> Only because I don<span class="token string">'t want it to 
cause gross errors in my interpretation of the statistics . I will identify unique aspects of the data such as data values that cluster or show some 
unusual shape. So, step 1 of my Exploratory Data Analysis will be to preform descriptive statistics to look at the distribution of the data, checking 
the range, frequency, and shape of the data.;

***DESCRIPTIVE STATISTICS***
The importance:
	- Looking at the range of values and demonstrate the data'</span>s spread of dispersion<span class="token punctuation">.</span>
	<span class="token operator">-</span> Verify the number of times each value appears by looking at frequency<span class="token punctuation">.</span>
	<span class="token operator">-</span> Estabilish the shape of your <span class="token keyword">data</span> by determining <span class="token keyword">if</span> the <span class="token keyword">data</span> is symmetric or skewed<span class="token punctuation">,</span> meaning that the distribution has a longer tail either to the
	left or the right<span class="token punctuation">.</span>
	<span class="token operator">-</span> Check for outliers<span class="token punctuation">.</span>		
Measures of Variability <span class="token punctuation">(</span>spread of dispersion<span class="token punctuation">)</span>: the IQR is the difference between the <span class="token number">25</span>th and <span class="token number">75</span> percentiles so the upper and lower <span class="token number">25</span><span class="token punctuation">%</span> of the <span class="token keyword">data</span> 
<span class="token punctuation">(</span>possible outliers<span class="token punctuation">)</span> are removed<span class="token punctuation">.</span> Variance is a measure of variability of the <span class="token keyword">data</span> around the mean<span class="token punctuation">.</span> Standard deviation indicates how much variation
there is from the mean<span class="token punctuation">,</span> or measuring how spread out the <span class="token keyword">data</span> is<span class="token punctuation">.</span> A low SD means <span class="token keyword">data</span> points tend to be very close to the mena and a high SD means 
the <span class="token keyword">data</span> is spread out over a larger range of values<span class="token punctuation">.</span>	<span class="token punctuation">;</span>

<span class="token comment">* Comparing the avg and median of income levels and their overall amount of money spent ;</span>
<span class="token keyword">PROC MEANS</span> <span class="token keyword">data</span><span class="token operator">=</span> work<span class="token punctuation">.</span>marketing12pc maxdec<span class="token operator">=</span> <span class="token number">2</span> fw<span class="token operator">=</span> <span class="token number">10</span> printalltypes
		n mean median std var q1 q3<span class="token punctuation">;</span>
	class Income_Levels<span class="token punctuation">;</span>
	var mnttotal<span class="token punctuation">;</span>
	title <span class="token string">'Descriptive Statistics Using PROC MEANS'</span><span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>
title<span class="token punctuation">;</span>

<span class="token comment">* Comparing the avg income levels to each other ;</span>
<span class="token keyword">PROC MEANS</span> <span class="token keyword">data</span><span class="token operator">=</span> work<span class="token punctuation">.</span>marketing12pc maxdec<span class="token operator">=</span> <span class="token number">2</span> fw<span class="token operator">=</span> <span class="token number">10</span> printalltypes
		n mean median std var q1 q3<span class="token punctuation">;</span>
	class Income_Levels<span class="token punctuation">;</span>
	var income<span class="token punctuation">;</span>
	title <span class="token string">'Descriptive Statistics Using PROC MEANS'</span><span class="token punctuation">;</span>
<span class="token keyword">run</span><span class="token punctuation">;</span>
title<span class="token punctuation">;</span>

<span class="token operator">*</span> I will include a histogram to view a visual representation of the frequency distribution of the <span class="token keyword">data</span> and to see of my sample has a normal distribution<span class="token punctuation">.</span>
The frequencies are represented by bars and each<span class="token punctuation">,</span> represents a group of values <span class="token punctuation">(</span>bins<span class="token punctuation">)</span><span class="token punctuation">.</span> The heights of the bards represent the frequency of values <span class="token operator">in</span> the 
bins<span class="token punctuation">.</span> Skewness and kurtosis measures aspects of the shape of the distribution<span class="token punctuation">,</span> the closer these statistics are to <span class="token number">0</span><span class="token punctuation">,</span> the more normal or symmetric the 
<span class="token keyword">data</span> and the <span class="token keyword">data</span> is shaped like the normal distribution<span class="token punctuation">.</span> 

Skewness measures how your <span class="token keyword">data</span> is spread out<span class="token punctuation">,</span> on one side<span class="token punctuation">,</span> of the mean than on the other<span class="token punctuation">.</span> So
left skewed will have the lower values on the left side of the curve<span class="token punctuation">,</span> <span class="token keyword">then</span> the higher values are on the right side of the curve<span class="token punctuation">.</span> When the statistics is
negative<span class="token punctuation">,</span> the <span class="token keyword">data</span> is left skewed and when its positive<span class="token punctuation">,</span> the <span class="token keyword">data</span> is right<span class="token operator">-</span>skewed<span class="token punctuation">.</span> A left skewed distribution tells us that the mean is less than the
median and right skewed shows that the mean is greater than the median<span class="token punctuation">.</span>

Kurtosis measures the tendency of the <span class="token keyword">data</span> to be concentrated either toward the center or the tails of the distribution<span class="token punctuation">.</span> It<span class="token string">'s a measure of tail 
thickness. The closer the statistic is to 0 the closer the tails of the data resemble the tail thickeness of the normal distribution. 
Platykurtic: A negative kurtosis means the data has lighter tails than in a normal distribution and data is less heavily concentrated about the mean.
Leptokurtic: A positive kurtosis statistic means the data has heavier tails and is more concentrated about the mean than a normal distribution. This is
an outlier-prone distribution.
If the distribution is symmetric, a leptokurtic distribution tends to have a higher peak than the normal, with an excess of values near the mean and in
the tails, but with thinner flanks. Distributions that are asymmetric also tend to have nonzero kurtosis. 
;

* Ways to check the normality of your data are: if the mean and median are nearly equal, if the skewness and kurtosis statistics are close to 0, histograms
and other graphical tools to visually asses the data.;
** Determine if the variables income and mnttotal are noramlly distributed. Determine the min max mean standard deviation for the variables.  ;
proc univariate data= work.marketing12pc noprint;
	var income ;
	histogram income  / normal (mu=est sigma=est noprint);
	inset min max skewness kurtosis / position=ne;
	probplot income  / normal(mu=est sigma=est);
	inset min max skewness kurtosis;
	title '</span>descriptive statssss<span class="token string">';
	run;
	title;
** The distribution for income looks approximately normal. 

 
-- Now, I want to use inferential statistics to calculate the standard error of the mean and confidence intervals for the mean.
For condifence intervals for the mean I will define the distribution of sample means and the central limit theorem, calculate and interpret the standard
error of the mean and confidence intervals for the mean, and use the MEANS procdedure to generate the standard error of the mean and confidence intervals
for the mean. Any sample statistic has some variability so the key is to understand and estimate this variability. The variance of the sample mean refers
to how much the value of the sample mean varies from sample to sample. To figure out the variability of my sample statistic I will calculate the standard
error. 1 standard error calculation I will preform is standard error of the mean because I will be able to measure the varibility of the sample mean and see
the estimate of how much I can expect the sample mean to vary from sample to sample, since different samples yeild different estimates of the mean for the
same population. The smaller the standard error of the sample mean, the more precise the sample mean is as an estimator of the population mean.

Another possible point estimator that directly accounts for the natual variability from sample to sample would be the inteval estimator because it will
give me a range of values that is likely to contain the population mean. An interval estimator that I will use is the confidence interval to show a range of
plausible values for the unkown population mean. I will use the 95% confidence level because I expect 95% of all sample means to fall within 2 standard 
errors of the population mean. If my data didnt look like it came from a normal distribution then to satisfy the assumption of normality I would apply the
central limit theorem. The CLT means the distribution of sample means is appx. normal regardless of the population distributions shape, if the sample size
is large enough (30 obs), I would use more observations is the data is heavily skewed or fewer if the data is symmetric. To re-itteriate, the central limit
thereom states that the distribution of sample means is appx. normal if the population is normal, of if the sample size is large enough. This is not related
to the standard deviation.

I need to asses that the averge income is 60,000 which is a guess I made. Assuming I already created a sample of the data, then I have already determined if
the sample data is appx. normal, calculated the sample mean and sample standard deviation. But I dont know the actual population mean and pipulation standard
devuation. So I will need to calculate the standard error of the mean for the whole data so that I can addes how precise my sample estimate is. I also need
to calculate the confidence interval for the mean so that I can estimate the population mean while taking into account the variability of the sample statistic.

The PROC MEANS will caclucalte a 95% confidence interval for the mean of the variable I choose. Below the number of observations, the mean of the data, the
standard error of the mean and the confidence interval for the mean is displayed.  ;

PROC SURVEYSELECT data=mark.newmarketing
	seed=13094425
	method=srs
	samprate=0.05
	out=work.marketing12pc;
run;


PROC MEANS data=work.marketing12pc maxdec=4
		n mean stderr clm;
	var income;
	title '</span><span class="token number">95</span><span class="token punctuation">%</span> Confidence Interval for Income<span class="token string">';
run;

* The sample mean is appx. 51,000 no way near 60,000 and the confidence interval of 47244.4466 to 54795.1390 also does not include 60,000. This is evidence that the
income of 60,000 is not at least a reasonable candidate for the true population mean. So I can conclude that 60,000 is not a reasonably likely sample mean for a random
sample drawn from a population centered around 60,000. Note: This logic only applies because the population of means is normally distributed and the standard deviation
of the sample is a good estimate of the standard deviation of the population. (hypothesis testing). My interval did not contain the targeted value, so the true average 
income is significantly different. I am 95% confident that the true mean income for the population is somwhere between 47244.4466 and 54795.1390.  ;
 
* Now I will conduct the key technnique of inferential statistics: hypothese testing to use sample data to evaluate my question about this population. A hypothesis test
uses sample data to evaluate a question about a population which makes it easier to conclude things about a population based on our sample data. I will either reject 
or fail to reject a statistical hypothesis that is a statement about the value of a population parameter. I will
1. Design and conduct a hypothesis test
	a. define null and alternative hypothesis: null is what you assume to be true (a big p-value) and the alternative is what you attempt to demonstrate.
		p-value of 0.3 is large (fail to reject) and .03 is small (reject)
	b. select the amount of evidence needed to reject the null hypothesis, a common significance level is .05 1 chance in 20.
	c. collect the data
	d. evaluate the data using the decision rule.
	
2. Use the p-value to determine statistical significance
3. Use the UNIVARIATE procedure to preform a statisical hypothesis test
4. Perform a one-sample, two-sided t-test to determine if the population mean is signficantly different from a known value ;

 
  
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 


****PREPARING DATA****;
**Which category produced the most profits? ;
proc sql;
create table mark.bigstcategory as
select
	sum(mntfishproducts) as total_fish format dollar10.
,	sum(mntmeatproducts) as total_meat format dollar10.
,	sum(mntfruits) as total_fruits format dollar10.
,	sum(mntsweetproducts) as total_sweets format dollar10.
,	sum(mntwines) as total_wine format dollar10.
,	sum(mntgoldprods) as total_gold format dollar10.
from mark.marketing
;
quit;

**The most profitable categories are wine, meat, then gold. Next, what percentage have kids? ;
proc sql;
create table KidsAnalysis as
select distinct
	count(*)
,	(select count(*) from mark.marketing where mntwines ne 0) as CustomerWineCount
,	(select count(*) from mark.marketing where mntmeatproducts ne 0) as CustomerMeatCount
,	(select count(*) from mark.marketing where mntgoldprods ne 0) as CustomerGoldCount
,	(select count(kidhome) from mark.marketing where mntwines ne 0 and kidhome ne 0) as KidsWine
,	(select count(kidhome) from mark.marketing where mntmeatproducts ne 0 and kidhome ne 0) as KidsMeat
,	(select count(kidhome) from mark.marketing where mntgoldprods ne 0 and kidhome ne 0) as KidsGold
,	calculated KidsWine / calculated CustomerWineCount as KidsWinePercentage
,	calculated KidsMeat / calculated CustomerMeatCount as KidsMeatPercentage
,	calculated KidsGold / calculated CustomerGoldCount as KidsGoldPercentage
from mark.marketing;
quit;


**Break up the incomes into 3 brackets: low ($20,000 or less), middle ($20,001 to $44,999) and upper ($45,000 to $113,734)
  Break up into children or no children by adding kids and teen together
  Break up the married and single selections and other marital statuses as '</span>Other<span class="token string">'
  for Married column 1 = yes, 2 = no and 3 = not disclosed;
proc sql;
create table IncomeLevels as
select
	*  
,	case 
		when income between 0 and 20000 then '</span>low<span class="token string">'
		when income between 20001 and 44999 then '</span>medium<span class="token string">'
		when income between 50000 and 113734 then '</span>high<span class="token string">'
	else '</span>N<span class="token operator">/</span>A<span class="token string">' end as Income_Levels
,	case
		when kidhome ne 0 then 1
		when teenhome ne 0 then 1
		else 0 end as Has_Children
,	case
		when mntwines ne 0 then 1
		when mntmeatproducts ne 0 then 1
		else 0 end as purchased_meat_or_wine
,	sum(NumWebPurchases) as web_purchases
,	sum(NumCatalogPurchases) as catalog_purchases
,	sum(NumStorePurchases) as store_purchases
,	sum(NumWebVisitsMonth) as monthly_web_visits
from mark.marketing
;
quit;

PROC PRINT data=IncomeLevels (obs=10);
run;

PROC FREQ data=IncomeLevels;
table marital_single * has_children;
run;


** How much did each income level spend on wine on average ;
proc sql;
create table IncomeAnalysis as
select distinct
	case
		when Income_Levels not in ('</span>medium<span class="token string">', '</span>high<span class="token string">', '</span>N<span class="token operator">/</span>A<span class="token string">') then round(coalesce(avg(mntwines),0),2) else 0 end as Low_Income_Avg_Spent
,	case
		when Income_Levels not in ('</span>low<span class="token string">', '</span>high<span class="token string">', '</span>N<span class="token operator">/</span>A<span class="token string">') then round(coalesce(avg(mntwines),0),2) else 0 end as Medium_Income_Avg_Spent
,	case
		when Income_Levels not in ('</span>medium<span class="token string">', '</span>low<span class="token string">', '</span>N<span class="token operator">/</span>A'<span class="token punctuation">)</span> <span class="token keyword">then</span> round<span class="token punctuation">(</span>coalesce<span class="token punctuation">(</span>avg<span class="token punctuation">(</span>mntwines<span class="token punctuation">)</span><span class="token punctuation">,</span><span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">,</span><span class="token number">2</span><span class="token punctuation">)</span> <span class="token keyword">else</span> <span class="token number">0</span> end as High_Income_Avg_Spent
from IncomeLevels
group by Income_Levels<span class="token punctuation">;</span>
<span class="token keyword">quit</span><span class="token punctuation">;</span>




<span class="token comment">* Viewing the number of visits;</span>
<span class="token keyword">proc sql</span><span class="token punctuation">;</span>
select
	sum<span class="token punctuation">(</span>marital_married<span class="token punctuation">)</span> as total_married
<span class="token punctuation">,</span>	sum<span class="token punctuation">(</span>marital_single<span class="token punctuation">)</span> as total_single
<span class="token punctuation">,</span>	count<span class="token punctuation">(</span><span class="token operator">*</span><span class="token punctuation">)</span> as total_customers
<span class="token punctuation">,</span>	sum<span class="token punctuation">(</span>NumStorePurchases<span class="token punctuation">)</span>
<span class="token punctuation">,</span>	sum<span class="token punctuation">(</span>NumCatalogPurchases<span class="token punctuation">)</span>
<span class="token punctuation">,</span>	sum<span class="token punctuation">(</span>NumWebPurchases<span class="token punctuation">)</span>
<span class="token punctuation">,</span>	sum<span class="token punctuation">(</span>NumWebVisitsMonth<span class="token punctuation">)</span>
<span class="token punctuation">,</span>	<span class="token punctuation">(</span>calculated total_customers <span class="token operator">/</span> calculated total_single<span class="token punctuation">)</span>  as percentage_single
<span class="token punctuation">,</span> 	<span class="token punctuation">(</span>calculated total_customers <span class="token operator">/</span> calculated total_married<span class="token punctuation">)</span> as percentage_single
	
from IncomeLevels<span class="token punctuation">;</span>
<span class="token keyword">quit</span><span class="token punctuation">;</span>


<span class="token comment">/* Below are the total of purchases and the total monthly web visits.
web_purchases	catalog_purchases	store_purchases	monthly_web_visits
9042			5833				12841			11768					*/</span>

<span class="token comment">* How many customers purchased meat or wine? ;</span>
<span class="token keyword">proc sql</span><span class="token punctuation">;</span>
select count<span class="token punctuation">(</span><span class="token operator">*</span><span class="token punctuation">)</span> 
from incomelevels
where mntwines <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> <span class="token keyword">quit</span><span class="token punctuation">;</span>th <span class="token punctuation">[</span>StackEdit<span class="token operator">+</span><span class="token punctuation">]</span><span class="token punctuation">(</span>https:<span class="token operator">/</span><span class="token operator">/</span>stackedit<span class="token punctuation">.</span>net<span class="token operator">/</span><span class="token punctuation">)</span><span class="token punctuation">.</span>
</code></pre>

