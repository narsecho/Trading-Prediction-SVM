# Trading-Prediction-SVM

1. Data set:
From yahoo.finance or any other similar site , select 20 major companies Comp1 ... Comp20 on the US stockmarket, and download their daily stock prices at closing time during the the time period.
TIMEPER = 2015-2016-2017-2018; Consider Comp20 as the "target" company we want to monitor. Let T1 ... TN be the calendar dates at which the US stock exchange was open during TIMEPER;
N should be in the range 1000 - 1020;
Rename these calendar dates as t=1, 2, ... N;
Let Sj(t) be the stock price of company Compj (at closing time).

Prediction task:
On each day "t" , S20(t+1) is UNKNOWN to all stock traders and we want to predict if the unknown S20(t+1) will be up or down with respect to the currently known S20(t);
To compute our prediction on day t, we will use the currently known past data for our 20 stock prices over the last 10 days up to day "t" , with day "t" included.

2. Pre-Processing: 
Replace missing values Sj(t) by the mean of two actual values closest to time t. If there are too many missing values in Sj, discard the stock Compj and download another stock
For each stock price Sj(t) compute the rate of return:
Yj(t) = (Sj(t) - Sj(t-1))/ Sj(t-1)
only use the time series Y1(t) ... Y20(t) defined for 2 <= t <= N.

3. Create cases and feature vectors:
for each day t ≥ 10, the recent past of the series Yj will be the 1x10 line vector Vj (t) = [ Yj(t-9), , Yj(t-1) , Yj(t) ]
at each day t >=10, the current past of our 20 time series Y1 Y20 will be recorded by a long line vector Xt of length 200 defined by 
Xt = [ V1(t) , V2(t) , V20(t) ] = [ Xt(1), Xt(2), ... Xt(200) ]
This means that the 20 line vectors V1(t) ... V20(t) are consecutively listed in a long line vector.
For each day t with 10 ≤ t ≤ N-1 , define the case "t" by its feature vector Xt which lists the 200 features Xt(1) ... Xt(200) of case "t"; then have a total of (N-10) cases in this data set; Generate this data set of (N-10) cases , where each case "t" is described by the feature vector Xt of length 200. 

4. Create two disjoint classes of cases CL0=LOW and CL1=HIGH
For day t, the value TARG(t)=Y20(t+1)=(S20(t+1)-S20(t))/S20(t) is currently unknown to traders
define true class(case "t") = HIGH if TARG(t)>= 0.6%; =LOW if TARG(t)<0.6%;
NOTE that on day t, the true class of case "t" is cuurently unknown;
Generate the column vector Z=Z(10),Z(11),...,Z(N-1) of true class values by Z(t)=trueclass(case"t")
give the percentages size(HIGH)/(N-10) and size(LOW)/(N-10)

5. PCA analysis:
compute the correlation matrix CORR of the 200 features Xt(1) ... Xt(200); 
Each feature takes N-10 values, but CORR is a 200x200 matrix; display CORR in an excel table, plot the 200 eigenvalues λ1 > λ2 > ... > λ200 of CORR.
Plot the percentages of explained variance PEV(k) = ( λ1 + ... + λk ) / ( λ1 + ... + λ200 )
Plot PEV(k) versus k= 1... 200. Determine the number h of principal components which gives PEV(h) = 90%
Compute the first h principal components Ut(1) ... Ut(h) of Xt for each case"t", For each case "t", replace the old feature vector Xt by the new feature vector Ut
Ut = [ Ut(1) ... Ut(h) ] which has dimension h

6. Training and test sets
6.1 Separately split each class HIGH, LOW into trainingset + testset , in the proportion 85%,15%;
6.2 If the ratio size(HIGH) / ( size(LOW) is not in the interval 40%, 60% , rebalance the classes by increasing the size of the smallest class. Make sure that you increase separately the training set and the test set of the smaller class.
6.3 After 6.1 + 6.2 define the full training set and the full test set by regrouping across the two classes.

7. Apply first a linear SVM to classify the training set into HIGH and LOW. Do this for 10 values of the error wheight coefficients C
Evaluate the accuracies testAcc and trainAcc on the test set for each C; Evaluate the ratio testAcc / trainAcc and the percentage of support vectors %support for each C
Give all these results in a table.

8. plot these 4 evaluations versus C. Select 3 best potential values C1 C2 C3 of C. Redefine a new set of 12 values of C centered around C1 C2 C3
Repeat (7) with this new list of 12 values of C and repeat interpretation.

9. Apply SVM with a radial kernel to classify the training set into HIGH and LOW.
9.1 use hilbert distances as explained in the course to discover a potential range of values for the parameter gamma of the radial kernel; select 4 values of gamma in that range
9.2 select 10 values of the error wheight coefficients C;
9.3 Apply radial SVM for each one of the 10x4 = 40 pairs (C,gamma); Evaluate the accuracies testAcc and trainAcc for each pair {C, gamma}; Evaluate ratio testAcc / trainAcc and %support vectors for each pair {C,gamma}
9.4 Give all these results in 4 tables of size 10x4; Interpret results to select 5 best potential pairs {C,gamma}; Compare results to best linear SVM found earlier.

10. repeat the exploration ( 9) using new pairs (C,gamma) around each one of 5 the best potential pairs found in (9).

11 select the best SVM classifier SVM*discovered sofar in 7 8 9 compute the confusion matrix of the SVM*; interpret this confusion matrix for SVM* compute the percentage of support vectors within class LOW , and do the same for class HIGH.
