# [Facebook-V-Predicting-Check-Ins](https://www.kaggle.com/c/facebook-v-predicting-check-ins)
#(Under Updation)
## Abstract:
> In the past decade, Facebook has become one of the most used social networking site in the world with around 2.2 billion monthly active users in 2018. The user check-ins on Facebook and other social media platforms have seen a colossal increase in popularity, thus the associated task of predicting check-in locations. Ability to predict not only can improve businesses outreach but also provides an opportunity to scale their operations. In this project, we will try to train a model on an artificial world to predict up to three check-in locations.

## 1.Data Description
The training dataset approximately has 30 million training instances, each of which represents an anonymous check-in registered in a 10 km by 10 km square map of an artificial world. Each of those training instances is labeled by a place_id which has roughly 100,000 unique values.
The features of the training set are discussed below:
1. Row_id: It describes the record number of the corresponding check-ins
2. X: It denotes the x-coordinate of the check-in within the square grid i.e. bounded by [0,10]
3. Y: It denotes the y-coordinate of the check-in within the square grid i.e. bounded by [0,10]
4. accuracy: We are still not sure what this feature means. For now, we are assuming that since the data was collected from primarily mobile devices, it could be some error w.r.t. the x and y coordinates of the check-in.
5. time: It denotes the time-stamp in minutes when the user in the (x,y) location had a check-in to the particular location.
6. Place_id: It specifies the id of the business/location. This is the target variable we are trying to predict.

The test dataset is also has similar attributes only without the place_id label for each test-instance. For each test-instance we have to predict upto three most likely places.

## 2. Data PreProcessing
The dataset we are working with is very large i.e. contains roughly 30 million training records and roughly 100k unique labels(place_id). This enormous size of the dataset is very difficult to handle so we first want to divide the dataset into grids of 1km by 1km i.e. into 100 smaller square grids. Now, it is relatively easy to use data exploration methods on this smaller grids to identify patterns and trends in the data. Also, the time attribute can be divided into more features: hour of day, day of week, day of month and month of year. These extra features depicts the temporal behavior of the dataset. For example, a particular event/location could be more popular during the day, whereas some other event/location could be more popular during the night. Similarly, a certain event might gain more popularity during weekends in contrast to its popularity during weekends, and vice versa.

## 3. Data Exploration
Due to large size of the data, it was not possible for us to use it completely for exploratory analysis. Instead we have taken a representative sample from the dataset and used it for the analysis shown below. The representative sample we chose was the first grid from the top left corner of size 1km by 1km. We assume that the analysis we did for this sample population will also hold true for the entire population. The representative sample grid of size 1km by 1km contains 280k training instances with around 5000 unique place_ids. We did the following analysis on the sample grid:

#### 3.1 Frequency of place_id’s 
We calculated the frequency of each place_id from the training sample and then plotted it in decreasing sorted order i.e. the place_id with the highest frequency was numbered as 1, then the next highest was numbered 2 and so on as shown in the below Figure. 

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/freqplot_place_id.jpg)

From the figure, we observe that events/locations have varying popularity. By popularity of an event/location, we refer to the fre- quency of user’s check-in to that particular event. We also notice that the there are only a few very events have a high popularity, some events have an average popularity whereas most other events have very low popularity.

#### 3.2 Scatter plot of x and y

We have plotted the scatter plot of the (x,y) coordinates of top 15 most popular places on our training sample. In the scatter plot shown in Figure 2, each color represents all the check-ins of a particular event,. We see that the events are clustered w.r.t. to the (x,y) coordinates. It is also evident from the scatter plot that the variance of x is larger than the variance of y, this may be due to horizontal nature of the streets from which the users check-in into a particular event. We can also observe some outliers from the scatter plot, these may be primarily due to the use of mobile devices for check-in.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/Scatter_plot.jpg)

#### 3.3 Frequency vs time of day plots

We have plotted the frequency versus time of day for the 5 most popular events. We see that different events are popular during different times of the day. For example, locations like recreation parks are more popular during the day, concerts are more popular during the nights. In the plots shown in below Figure, each color represents a particu- lar place_id. For a simple analysis, we have selected the 5 most popular events and then observed its popularity( i.e. frequency of check-in) hourly during a period of 24 hours. We have founds results in correspondence to our initial real world analysis of events.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/hour.jpg)

Plotting most popular clusters using the hour component as our third variable give us further insight as Idea behind using hour component is the daily cycles will be visible to us.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/Top5PlaceIds.png)

Below figure shows variation by day of week. It gives us insight that some businesses are busier on the weekend than others and Full Weekly trend can be observed, predictions based on trends can be made.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/Top5PlaceIdsdaywise.png)

#### 3.4 Cluster analysis using DB-Scan on location co-ordinates

Density-based spatial clustering[2] of applications with noise (DB- SCAN) is a very popular data clustering algorithm that is commonly used when you have highly dense data and its difficult to segre- gate based on normal classifiers. Since this reasoning very much resonates with our dataset, we, therefore, tried to explore working on this technique on our data set. Another reason to choose this model was our data set contains more than 100,000 unique placed therefore it was logical to bring down to a lower value. In layman terms, DBSCAN groups together points which are close to each other based on a distance measurement (eps) and a min- imum number of points(to decide core point). Even though this technique is very powerful in finding dense regions and cluster them together, one of the drawbacks is we have to provide both distance and number of points. This requires tuning of these param- eters as they are highly dependent on data and its structure. We did some of our own analysis on 10% of data as it was computationally not feasible to do such kind of exploration on whole data set. Our exploration started from [7] where author suggested using nearest neighbour technique for finding best eps value.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/nearest_neighbour.png)



#### REFERENCES
1. Leo Breiman. 2001. Random forests. Machine learning 45, 1 (2001), 5–32.
2. Martin Ester, Hans-Peter Kriegel, Jörg Sander, Xiaowei Xu, et al. 1996. A density- based algorithm for discovering clusters in large spatial databases with noise.. In Kdd, Vol. 96. 226–231.
3. Gongde Guo, Hui Wang, David Bell, Yaxin Bi, and Kieran Greer. 2003. KNN model- based approach in classification. In OTM Confederated International Conferences" On the Move to Meaningful Internet Systems". Springer, 986–996.
4. Chih-Wei Hsu, Chih-Chung Chang, Chih-Jen Lin, et al. 2003. A practical guide to support vector classification. (2003).
5. Daniel Lowd and Pedro Domingos. 2005. Naive Bayes models for probability estimation. In Proceedings of the 22nd international conference on Machine learning. ACM, 529–536.
6. Sonali B Maind, Priyanka Wankar, et al. 2014. Research paper on basic of artificial neural network. International Journal on Recent and Innovation Trends in Computing and Communication 2, 1 (2014), 96–100.
7. Nadia Rahmah and Imas Sukaesih Sitanggang. 2016. Determination of optimal epsilon (eps) value on dbscan algorithm to clustering data on peatland hotspots in sumatra. In IOP Conference Series: Earth and Environmental Science, Vol. 31. IOP Publishing, 012012.

