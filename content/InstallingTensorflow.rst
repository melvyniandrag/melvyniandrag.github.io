Installing Tensorflow! Notes to self.
##########################################

:date: 2017-10-05
:modified: 2017-10-05 22:20
:tags: tensorflow docker python machine-learning
:category: tensorflow
:slug: Installing-tensorflow 
:authors: Melvyn Drag
:summary: How I installed tensorflow - notes to self so I dont forget. 

*****************************
Introduction
*****************************
I've been wanting to install tensorflow for a while and play around with it. Then a cool adversarial machine learning project was on Kaggle, so I entered it - the contest used tensorflow! Unfortunately (or so I thought at the time) it also used docker. And so I read a bit about docker, read a tiny bit about tensorflow, read a couple of papers on adversarial machine learning, but it was no use. I felt drowned in a sea of new stuff, and my full time job and personal obligations didn't allow me to invest the necessary time to see what was going on. Days after the contest ended, it all made sense.  

***********************************
Installing Tensorflow with Docker
***********************************
I have recently installed opencv on my new computer, and I installed opencv to work with cudnn 5. Unfortunately, the tensorflow page says to use cudnn6, or else build the library from sources (no thanks). But it also offered a docker install. I said to myself, it seems easier than messing around with cuda libraries and modding my PATH and LD_LIBRARY_PATH, so I went for docker.

It's pretty much a virtual environment for your process that installs all of the necessary libraries and tools for the code to run! And you can clone environments from a repository online where users can submit their configurations, kinda like github but for environments. The folks at tensorflow maintain their own docker containers, including one for gpu support - so no cudnn upgrade / opencv break necessary!

I installed nvidia-docker following the steps on the website, and then I just started using the tensor flow image!

This will give you a shell to enter commands in:

nvidia-docker run -it  gcr.io/tensorflow/tensorflow:latest-gpu bash

This will bring up a jupyter notebook with some tutorial stuff. 

nvidia-docker run -it -p 8888:8888 gcr.io/tensorflow/tensorflow:latest-gpu

So far as I can tell the -p 8888:8888 routes the container localhost to your localhost so you can access the jupyter notebook in your browser. It also seems that if you follow the container name with a program, like bash, then that program is run in the indicated environment.

I saw a warning online about using the latest container image, and I may look for an older stable on in the future if I hit a wall when playing around with tensorflow. Otherwise, it seems like smooth sailing. If only I had figured out what docker was all about while the contest was running, maybe I could have focused more on playing around with tensorflow. You live and you learn!
