# w251_hw9 by abhisha@berkeley.edu

This homework is based off https://github.com/MIDS-scaling-up/v2/tree/master/week09/hw

#### 1. How long does it take to complete the training run? (hint: this session is on distributed training, so it will take a while)

The training took around 22-24 hours to run on the 2 v100 machines

#### 2. Do you think your model is fully trained? How can you tell?

Yes, I believe it finished, if we look at the nohup file (we had set the training step size to 50k instead of 300k) - we see the bottom of the file explicitly indicates that training has finished. Moreover, if we look at the tensorboard, we notice that it is no longer updating the number of steps beyond 50k in any of the graphs. This is enough proof the training has completed.

#### 3. Were you overfitting?


#### 4. Were your GPUs fully utilized?

#### 5. Did you monitor network traffic (hint: apt install nmon ) ? Was network the bottleneck?

Network did NOT seem to be the bottleneck based on nmon stats. It seemed like the network 

#### 6. Take a look at the plot of the learning rate and then check the config file. Can you explan this setting?



#### 7. How big was your training set (mb)? How many training lines did it contain?

#### 8. What are the files that a TF checkpoint is comprised of?

#### 9. How big is your resulting model checkpoint (mb)?

The entire "best model" folder was around 4.1G, whereas the actual checkpoint itself was around 4KB. Below are some additional details:
![model_size](https://github.com/abhisha1991/w251_hw9/blob/master/Final/ModelSize.PNG)

#### 10. Remember the definition of a "step". How long did an average step take?

From the bottom of the nohup file, we see that the average time taken for a step was 1.629 seconds

#### 11. How does that correlate with the observed network utilization between nodes?
