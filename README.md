# w251_hw9 by abhisha@berkeley.edu

This homework is based off https://github.com/MIDS-scaling-up/v2/tree/master/week09/hw

#### 1. How long does it take to complete the training run? (hint: this session is on distributed training, so it will take a while)

The training took around 22-24 hours to run on the 2 v100 machines

#### 2. Do you think your model is fully trained? How can you tell?

Yes, I believe it finished, if we look at the nohup file (we had set the training step size to 50k instead of 300k) - we see the bottom of the file explicitly indicates that training has finished. Moreover, if we look at the tensorboard, we notice that it is no longer updating the number of steps beyond 50k in any of the graphs. This is enough proof the training has completed.

#### 3. Were you overfitting?

If we look at both the eval_loss and training_loss, we observe that they both were converging to the same value (approx 1.6) after the training process / eval process ended. So we conclude that overfitting WAS NOT happening in the model.

![train](https://github.com/abhisha1991/w251_hw9/blob/master/Final/Train_Loss.PNG)

![eval](https://github.com/abhisha1991/w251_hw9/blob/master/Final/Eval_Loss.PNG)

#### 4. Were your GPUs fully utilized?

The performance varied over time, but it did seem like we were able to achieve a good continuous usage on the GPUs. 
![gpu](https://github.com/abhisha1991/w251_hw9/blob/master/Final/GPU.PNG)

#### 5. Did you monitor network traffic (hint: apt install nmon ) ? Was network the bottleneck?

Network did NOT seem to be the bottleneck based on nmon stats. It seemed like the network settings were accelerated under the covers by Horovod / NCCL / Infiniband - and because of this, the potential network bottleneck seemed to have vanished. The reason I say this is because, according to the instructions in the homework and these [docs](https://cloud.ibm.com/docs/cli?topic=cli-cli-virtual-servers), we set the "--network" parameter to be 1000 (ie, 1000 Mbps) when setting up the Vms. This translates to 125 MBps ethernet links between the Vms. So theoretically, if the data transfer rates between the Vms increased beyond 125 MBps, then we would have throttled on the networking side. However on close inspection of nmon, we find that speeds of 220 MBps were being achieved on the Vms. Hence, it seems like while there SHOULD have been a bottleneck due to the networking settings, however, in reality we bypassed this. Below is an image with the details of a screenshot capture of nmon. 
![nmon](https://github.com/abhisha1991/w251_hw9/blob/master/Final/nmon.png)

#### 6. Take a look at the plot of the learning rate and then check the config file. Can you explan this setting?

The learning rate has warm up steps = 8000, which means that we linearly increase the learning rate up to 8000 steps, as seen in the graph, and then we start decaying the learning rate after reaching that point. An explanation of warm up steps is given [here](https://datascience.stackexchange.com/questions/55991/in-the-context-of-deep-learning-what-is-training-warmup-steps). For NLP models, it seems like warm up steps has the benefit of slowly starting to tune things like attention mechanisms in your network. A learning rate of "2" given in the settings is likely some factor using which we are first increasing and then decreasing the learning over time.

#### 7. How big was your training set (mb)? How many training lines did it contain?

There were 2 training files - train.de (size = 678 M, lines = 4562012) and train.en (size = 607 M, lines = 4562012)

#### 8. What are the files that a TF checkpoint is comprised of?

I am getting the answer from [here](https://stackoverflow.com/questions/44516609/tensorflow-what-is-the-relationship-between-ckpt-file-and-ckpt-meta-and-ckp) partially. Basically, a TF model checkpoint consists of a few different files - index, meta and data. These are files used to construct the model again. The .ckpt-meta contains the structure of the computation graph of the model. The .ckpt-data contains the values for all the variables, without the structure. Typically, these 2 files are needed to regenerate the model.

#### 9. How big is your resulting model checkpoint (mb)?

The entire "best model" folder was around 4.1G, whereas the actual checkpoint itself was around 4KB. Below are some additional details:
![model_size](https://github.com/abhisha1991/w251_hw9/blob/master/Final/ModelSize.PNG)

#### 10. Remember the definition of a "step". How long did an average step take?

From the bottom of the nohup file, we see that the average time taken for a step was 1.629 seconds. In fact this makes a lot of sense. Our total training steps was 50k. If each training step takes 1.629 seconds, then the total time taken to train is 50k x 1.629 = 81,450 seconds or roughly, 22.625 hours. This is very well in expectation with what our observed training time was (22-24 hours)

#### 11. How does that correlate with the observed network utilization between nodes?

We would imagine that as the network utilization increases between the nodes, the more data is being transferred and the more often parameter updates are happening, hence we will be observing faster step times. If there is little usage of network between the 2 machines, then each machine is busy in terms of processing the model locally on the GPU, which means that the parameter updates are not taking place that often, which implies a slower step time.
