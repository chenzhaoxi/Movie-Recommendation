--- Based on Encoder and K-Means

====================================

Zhaoxi Chen | N18963553 | zc1134

Shangwen Yan | N17091204 | sy2160

## Abstract
recommender systems have become increasingly popular in recent years, and are utilized in a variety of areas including movies, music, news, books, research articles, search queries, social tags, and products in general. 
Here we implement a movie recommendation system based on Auto-Encoder and K-Meanse. The dataset is the popular one : movie-lens.

## Introduction
With the increasing development of technology and Internet, more and more customers choose to spend their spare time online instead of off-line. As a result, it's becoming of great importance to make a good use of the data produced by the consumers to offer a better consumption environment which is of great benefit both for merchants and customers.
As one of the results, recommender systems have become increasingly popular in recent years, and are utilized in a variety of areas including movies, music, news, books, research articles, search queries, social tags, and products in general. Here we implement a movie recommendation system using movie-lens dataset.



## Dataset
### Original dataset
Here we use the open dataset MovieLens. The relevant information is movies’ratings,genres,tags given by users
Number of users:7120，Number of movies: 27278，Number of ratings: 
__Files we use:__
|movie.csv|||
|-|-|-|
|movieId|title| genres|

|rating.csv||||
|-|-|-|-|
|userId|movieId| rating|timestamp|

### Training data
Since it's a clustering problem, it's more important to train the clusters. We only use 7100 users for traing, and make the data in to a torch of size (7100,27278,3) which means (number of users, number of movies, (rating, mean_rating, genres)). Since genres are given as text, we use one-hot-coding to turn it into binary int, and then decimal int.
![屏幕快照 2018-05-02 下午2.43.11.png](resources/C608ADA4760D219EC2FC89DEDCD83DB3.png =331x465)
### Testing data
We only use 20 users for testing case. And for those testing users, we split the most recently 20 ratings used to compared with the recommended movies, and the past records to go through the encoder and k-means to do the recommendation

## Architecture
The whole architecture is: first we use encoder to reduce the number of features which makes it simpler for the kmeans part, then use k-means to group the users into 9 clusters. After calculating the scores movies in each clusters, we recommend 30 movies which have not been seen by the users to them.
![屏幕快照 2018-05-02 下午2.43.21.png](resources/E0E82EAC9A56BF24EDAE8F58CA3FED98.png =814x322)
### Encoder part
![屏幕快照 2018-05-02 下午2.46.33.png](resources/475E22E4FE0B8816883BA6808A2A92BC.png =393x154)
First, we  train an autoencoder to make the decoded and input matrix as samilar as possible.

To make sure AE’s weights  are  orthogonal , we add penalty to __MSE loss:__

![屏幕快照 2018-05-02 下午2.46.06.png](resources/59F2F6757C032FE7F708DB14724E134E.png =345x38)
__epoch vs loss:__
![屏幕快照 2018-05-02 下午2.46.45.png](resources/A86D494400FAC494E9AF67DD8D215EBD.png =385x261)

The encoded tensor represents input tensor well, but with fewer features, which makes it easier for clustering  in k-means part.

![屏幕快照 2018-05-02 下午2.52.36.png](resources/0240B0E21D96089848A1DCE30FC35253.png =390x305)

###k-means part:
Then use encoded  tensor to do a k-means clustering which generates 9 clusters of user-groups.
For each cluster(user_group), we sum up users’ ratings for each movie and sort the movies by rating desc. 
Then use the top 50 movies’ genres to generate a word cloud to see which kind of movie is popular in that user group.
|word cloud|![cluster1.png](resources/4821ED74077B951BEE77951DC8DE6C23.png =432x288)|![cluster2.png](resources/F53227A097566779106E7534318307FE.png =432x288)|![cluster3.png](resources/0B84CDED01FDD11330EAE70C169761E6.png =432x288)|
|-|-|-|-|
|__bar plot__|![bar1.png](resources/EDCEA6F5F5B2C821818CAD9B1F9FFC80.png =901x655)|![bar2.png](resources/38A0893717D88238CCAB7DCAD3001320.png =904x642)| ![bar3.png](resources/3C7F4567D983653BAF916270BBCC6D80.png =907x656)|

|word cloud|![cluster4.png](resources/87DF664CAFFC3CC0E94158E92528A307.png =432x288)|![cluster5.png](resources/E59758C364180611F6A2DB41703FAFFC.png =432x288)|![cluster6.png](resources/1BDB08844298F51430D1EBB7B9959D96.png =432x288)|
|-|-|-|-|
|__bar plot__|![bar4.png](resources/BEF8CFBCD629D198C3F7005E57CE6DC2.png =897x644)|![bar5.png](resources/12DAB00A88A366217472153FBD81380D.png =905x643)|![bar6.png](resources/25625425DFA52CAE7CCDAB4934EE0CD7.png =899x636)|

|word cloud|![cluster7.png](resources/DA7CB38609B24DC5D76F3853A427EDF8.png =432x288)|![cluster8.png](resources/799E94B681F2B6E53C5A14FCE22EDA39.png =432x288)|![cluster9.png](resources/56333F709ECA35CE3B821B9A8F33C822.png =432x288)|
|-|-|-|-|
|__bar plot__|![bar7.png](resources/DC4FB35F9D3B4D1673BA0C7D05CC56AF.png =891x643)|![bar8.png](resources/8D03DFE5F7854B771EDD88944059CD68.png =897x643)|![bar9.png](resources/BE72256C787CE3D9E970C7AACCB2915D.png =890x646) |

Since ‘Drama’ and ‘Comedy show up frequently, we do a genre analysis among all movies(no clusters).
![bar.png](resources/B362A1760B89E988419C27499818CCCA.png =1080x720)
From the plot we could find some genres, like Drama and Comedy,  are ‘common types’ which are specified to most movies.

__after scaling:__
![屏幕快照 2018-05-02 下午3.11.44.png](resources/572A1F1F336E00E1EE71ED64E7DF71BD.png =906x387)
![屏幕快照 2018-05-02 下午3.11.57.png](resources/07BD65E78E7621C5B4FF53B144A5A5B5.png =900x375)
![屏幕快照 2018-05-02 下午3.12.08.png](resources/430DBB58EA90B059C0C062EE0B51D1B4.png =905x385)
![屏幕快照 2018-05-02 下午3.12.16.png](resources/AE0253C673AFE6A7B6D172388A448E64.png =906x389)
![屏幕快照 2018-05-02 下午3.12.24.png](resources/E930288C95161D99E7132B3DD9226E9C.png =882x380)
As we can see, the difference after scaling is kind of obvious.

## Recommen Movies
For every users go through the encoder and kmeans to put him into a cluster. Then recommend those movies with highest scores rated by users in the same cluster to him.
![屏幕快照 2018-05-02 上午1.27.32.png](resources/2C876F589BF21E090F48A4C2A510F7A2.png =817x677)
## Test Case
Recommend 30 movies to a user based on his past watching history.  Compare the results with those movies he  most recently watched.
here shoes one user's result:
![屏幕快照 2018-05-02 上午1.01.07.png](resources/B12A98C5E1B1B02477BB73F65712CDF8.png =702x327)
![屏幕快照 2018-05-02 上午1.01.19.png](resources/706EE04321FEDA5BBE1DB20EC858B839.png =688x170)
![屏幕快照 2018-05-02 上午1.01.33.png](resources/76AD975344B616206ED712BEB04B4C30.png =699x256)
__Recommend:__
Beauty and the Beast
Speed

__Actually watched:__
Beauty and the Beast
Speed 2



