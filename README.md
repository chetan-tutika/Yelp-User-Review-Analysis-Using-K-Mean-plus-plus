# Yelp-User-Review-Analysis-Using-K-Mean-plus-plus

Implemented stochastic Kmeans++ combined with Metropolis–Hastings algorithm for fast categorization of data
Improved the convergence rate up to 44 % by optimizing the initial seeding algorithm <br />

## Results
### Online Kmeans 
![image ukkduz](https://user-images.githubusercontent.com/41950483/50380357-fe887d00-0632-11e9-8aae-96ba89006619.png)<br />
Ideally batch size of the 1000 gives faster convergence with very less deviation in the loss. Since batch size of 500 also gives similar results we can consider 500 to be the ideal batch size. Stochastic process tries to estimate the loss with as less data points as possible. Hence, a batch size of 500 would be better than 1000<br />

### Online Kmeans++
![image ce96tz](https://user-images.githubusercontent.com/41950483/50380372-6e970300-0633-11e9-833c-cfcc209fc1ad.png)<br />
At k= 500, 1000 we get very less deviation in loss at different time steps. Since 500 gives
similar results as compared to a batch size of 1000, it is better to choose a batch size of 500 for
faster computations<br />

### Faster Seeding for Kmeans++
For faster initialization of the centroids the algorithm selected must approximate the posterior
distribution of the dataset with very few passes over the data. Some of the algorithms which
give good approximation of the distribution are the random walk Monte Carlo
algorithms(Metropolis–Hastings algorithm, Gibbs Sampling, etc). These algorithms make use of
the markov chains algorithm for a quick and efficient approximation.<br />
Hence, implementing an algorithm which makes use of markov chain would give us the best
results.<br />
We would like to approximate the probability distribution of our data by constructing a sequence
of markov chains and passing through them<br /> 

#### Algorithm
● Initialize an arbitrary centroid which lies on the data at random <br />
● Compute the probability distribution of all the data points with reference to the assigned
centroid<br />
● Pick a point at random from the data set to start the markov chain. This point will act as
the initial state in our sequence of points<br />
● Sample the next state form the dataset based on the probability distribution. Compute
the probabilities of staying in the same state and moving to the next state<br />
● Depending on the probabilities move to the desired state by introducing randomness in
the decision<br />
#### Our Contribution:
- Probability of State change:<br />
  - The state change or stay probability is computed by taking the sign of the
    distance between the present and future states to their respective centroids<br />
    `dist (x[j+1] - x[j] ) = D(x[j+1] ,C) – D([x[j] ,C)`<br />
    where D(x, C) is the distance square of the point x from the known centroid set C.<br />
- This gives a good estimation of movement in markov chains to different chains at
     time ‘t’. The estimation is similar to the Metropolis Hashing technique and gives
similar results.<br />
- By comparing the difference between the distances and taking the sign we can
say which state is farther to the centroid based on the sign value. The new state will be assigned based on the sign value which gives a good approximation of the distances to the centroids<br />
- The points with huge distances and with negative sign are mapped with the
function e^x. This maps (-inf, 0] to [0,1]
#### Update Rule
- The difference is computed between the next state and the current state in that
order. If the distance is positive, it means the next state is farther to the centroids
than the current state. Hence, we move to the next state<br />
- If the distance is negative the current state is farther than the next state. Hence,
we stay in the same state. The distance here is mapped from (-inf, 0] to [0, 1]. We
then sample from uniform distribution to decide the change in state based on the
mapped distance values<br />
`p(x[j+1] /x[j] ) = exp(dist(x[j+1] - x[j] ))`
- If the sampled uniform probability is less than the mapped distance, we
move to the next state else we stay in the same state<br />

![image 5hvbuz](https://user-images.githubusercontent.com/41950483/50380508-63de6d00-0637-11e9-959f-96f5729679f3.png)<br />

It can be observed the loss and other characteristics such as min and max over k
centroids is similar to the Kmeans ++ algorithm. This serves as a good indicator that the
proposed algorithm is compaarable to kmeans++<br />
The algorithm proposed is much faster than Kmeans++ since we approximate the
probability distribution with just a few passes over the data for each centroid<br />
The proposed method which uses markov chain approximation takes 42 seconds with a
batch of 500 data points to converge. For the same batch value, Kmeans++ initialization
takes 218 seconds. This difference is largely due to the initialization of the centroids<br />
