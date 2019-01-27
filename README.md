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

Based on our analysis of sorted distance to the 300th nearest neighbor, we saw that our data showed best clusters when we used eps value of 0.013 and density of 200.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/db_200.png)

## 4. Model Building
In this section, we will be discussing the different models we have used to predict the user check-ins. We have explored various stan- dard classifiers like k-Nearest Neighbors classifier(k-NN), Naive Bayes Classifier, Support Vector Machines(SVMs), Random Forest classifier and also some state of the art classifiers like Artificial Neu- ral Networks(ANNs). In the following, we will only mention the k-NN and the Naive Bayes classifier as they were used in our final formulation. The SVMs and the Random Forest classifiers would be later used as comparison metrics to our current model. We will also describe our Artificial Neural Network model in the extra credits section, where we would be explaining why we decided not to use it in our current model. We have used the these features to train our models : x, y, accuracy, timeof day, and dayof week.

#### 4.1 K-Nearest Neighbors classifier
The k-Nearest Neighbors classifier is one of the most simple ma- chine learning approaches which tries to classify new test point by deciding its label from the k-"closest" neighbors(nearest neighbors) based on some similarity metric. In our problem, we are trying the predict the place_id’s of an unknown test point by computing the k-nearest neighbors based on "Manhattan distance" and then using a weighted voting to predict its class label(place_id). The advantage of a weighted voting over a simple majority voting is that, by assigning weights based on their distance, i.e. points which have greater distance from the test point will be assigned lower weights and vice-versa. Thus, points closer to the test point will have more influence in the prediction of its class label as compared to points farther away. The Manhattan distance between two points x and y with dimension d.

The k-NN models seems to work astonishingly well in cases where it is not expected to perform well due to its simplicity, but the challenge is in tuning its parameters like weights of feature vec- tors,distance metric, weights during voting, and most importantly the value of k. We did not use any weights for the feature vectors here, we have just normalized the dataset. We have also tried the k-NN models using euclidean distance, Manhattan distance and Minkowski distance with p=3,5,7,9. The Manhattan distance out- performed other distance metrics by a large amount, thus we have considered it for training the k-NN models later. We have used two different k-NN models, where in both models, the similarity/distance was computed based on the Manhattan dis- tance, but the weights used during prediction were different. The first model used a weights according to 1/d2 while the other used 1/d, where d is the distance between the ith nearest neighbor and the test point.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/knn_plot.png)

We have tuned the value of k on our 1x1 representative sample (the same one we used for data exploration) which was used as a validation set. For both the k-NN models, values of k ranging from 1 to 100 was used to predict the top 3 classes for each test point and then using them to compute the final score (MAP@3, described in further detail in section : Model evaluation). The value of k which maximizes this score was chosen as the optimal value of k.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/knn_plot_2.png)

The optimal value of k for weight = 1/d was found out to be 18 and that for weight = 1/d2 is 27.

#### 4.2 Naive Bayes Classifier

Naive Bayes classifiers[5] are simple probabilistic classifiers based on Bayes theorem and they run on the assumption that there is strong independence between the features. Gaussian Naive Bayes is one of the Naive Bayes classifier that performs better with continuous values, a typical assumption that Gaussian Naive Bayes considers is that the continuous values asso- ciated with each class are distributed. Because of it performance on continuous values we have selected Gaussian Naive Bayes Classifier and it predicts the class which maximizes the probability of the class (in this case place_id) given its features.
For this problem, the posterior probability of the class i.e. place_id could be written as :
                      
                      P(place|x,y,accuracy,time) ∝ P(x,y,accuracy,time|place)P(place), 

Again the likelihood can be written as :
    
    P(x,y,accuracy,time|place) ≈ P(x|place) · P(y|place)· P(accuracy|place) · P(time of day|place)· P(dayof week|place).

In case of Gaussian Naive Bayes, we are assuming that probability of each feature given a class can be represented using a normal distribution. But we have found out that the features like day of month or month of the year cannot be represented using a normal distribution.

#### 4.3 Ensemble Method
The goal of ensemble methods is to combine the predictions of sev- eral base estimators built with a given learning algorithm in order to improve generalizability / robustness over a single estimator. For the final model, we would be using an ensemble methods to combine the aforementioned models. What we want to do is to train different weak classifiers and then combine them to form one strong classifier. Ensemble methods also increase the diversity of the model.
            
            H : X = (x, y, acc,timeof day, dayof week) → P(placeid) H(X) = argmax[αhknn1(X) + βhknn2(X) +γhnb(X)]

Here, we have used the concept of soft voting for classifiers. H represents a classifier, where it takes the feature vector X as the input and P(place_id) as output. These classifiers are combined by taking a weighted average of their probabilities and then picking the top 3 place_id’s with the three highest probabilities.
The weights α, β and γ are tunable, but we have chosen trivial values for the weights where all of them are set to one.

#### 4.3 Neural Networks

Artificial neural networks[6] are one of the main tools used in machine learning. As the âĂIJneuralâĂİ part of their name suggests, they are brain-inspired systems which are intended to replicate the way that we humans learn. Neural networks consist of input and output layers, as well as (in most cases) a hidden layer consisting of units that transform the input into something that the output layer can use.

![alt text](https://github.com/Riteshbansal/Facebook-V-Predicting-Check-Ins/blob/master/artificial_neural_network.jpg)

###### 4.3.1 Neural Network based model for Place_id prediction
The Neural network model we have trained has one input layer, 4 hidden layers and an output layers arranged in sequential order. Our hidden layers have 128, 512, 512 and 512 nodes respectively. The all the nodes in the input layer along with the 4 hidden layers have "relu" as their activation function, and the output node has "softmax" activation function, with as many output nodes as the number of unique place_ids available. We have used backpropagation algorithm with "Adam" optimiser, trying to minimize the "Categorical cross-entropy" loss. We have also implemented an early stopping criterior with patience value of 100 iterations, i.e. if there is no significant change in the model parameters for 100 iterations then the model will complete its training. We have used a batch size of 1000 and number of epochs being 1000.

## 5. Real Life Insights
Check-in prediction has found a lot of real world applications espe- cially for business. Below are the five real world cases which we believe this prediction can be very helpful:
- Segregating most popular places
- Check-In can benefit marketers
- Promoting the Business
- Location and diaspora based business strategy
- Prior arrangements can be made by authorities to handle eventuality.

## 6. Conclusion
The primary goal of this project was to build a classification model which could accurately predict the user check-ins for the artificial world. This task is challenging not only due to the colossal size of the dataset, but also due to the presence of high noise, variability in patterns and high number of unique classes.
Based on our analysis KNN though a very simple algorithm performed at par with more complex algorithms and considering limited availability of resources, it performed brilliantly. Neural Networks, though performed much better than all the models, could not be materialised because a model on all the data would have consumed considerable resources, for which there was scarcity.



#### REFERENCES
1. Leo Breiman. 2001. Random forests. Machine learning 45, 1 (2001), 5–32.
2. Martin Ester, Hans-Peter Kriegel, Jörg Sander, Xiaowei Xu, et al. 1996. A density- based algorithm for discovering clusters in large spatial databases with noise.. In Kdd, Vol. 96. 226–231.
3. Gongde Guo, Hui Wang, David Bell, Yaxin Bi, and Kieran Greer. 2003. KNN model- based approach in classification. In OTM Confederated International Conferences" On the Move to Meaningful Internet Systems". Springer, 986–996.
4. Chih-Wei Hsu, Chih-Chung Chang, Chih-Jen Lin, et al. 2003. A practical guide to support vector classification. (2003).
5. Daniel Lowd and Pedro Domingos. 2005. Naive Bayes models for probability estimation. In Proceedings of the 22nd international conference on Machine learning. ACM, 529–536.
6. Sonali B Maind, Priyanka Wankar, et al. 2014. Research paper on basic of artificial neural network. International Journal on Recent and Innovation Trends in Computing and Communication 2, 1 (2014), 96–100.
7. Nadia Rahmah and Imas Sukaesih Sitanggang. 2016. Determination of optimal epsilon (eps) value on dbscan algorithm to clustering data on peatland hotspots in sumatra. In IOP Conference Series: Earth and Environmental Science, Vol. 31. IOP Publishing, 012012.
