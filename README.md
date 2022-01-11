# Forest-Cover-Detection : MAP553 challenge 2021
## PILCER Michael, BRAUX-GUILLIN Valentin

Our objective is to predict the forest cover type, a categorical variable, from various features related
to an area. The metric to optimise is the accuracy on a test set with hidden labels. The main issue is
that we only have access to a small training set to build our model. This adds a particularly interesting
difficulty to the project as we can not simply rely on classical models to obtain a good performance

The challenge is taking place on the [kaggle competition](https://www.kaggle.com/c/map553-2021) of the course MAP553 of Ecole Polytechnique.

Capitalising on what we had discovered on wilderness areas during data exploration, we decided
to further improve our model by introducing conditional classifiers depending on area. We reproduced
the model of our first pipeline to areas 1,2,3 (with light adaptations when all cover types were not
present). Our second pipeline was thus composed of 12 classifiers (3 for the global prediction and
3 for each area). The prediction pipeline for an input was to construct the probability vector as a
barycenter between the global prediction and the prediction specific to its area. We trained all the
classifiers and grid-searched the optimal coefficients for the barycenters.

Finally, to complete this challenge, we used a meta-model instead of the grid-search to compute the prediction, we found that it generalized better to the test dataset.

We thus trained a feed forward neural network to choose a cover type from all the probabilities given by our
classifiers. For training, we had to be careful to split the train set once again. Indeed, if the neural
network had been trained on data used to fit the classifiers, it would have had an over-easy jobs,
probabilities being too close to 0 or 1. The network would then have a hard-time generalising on new
data. We chose to split the train set in two equal parts, one to train the classifiers (from scratch)
and the other one to train the neural network. Once the network was trained, we actually fitted the
classifiers with all the data available to maximise our accuracy.

We found out that two hidden layers of 32 neurons in between the inputs neurons and a dense
layer of 7 neurons gave good results. We chose ReLU activation functions for the hidden layers, and a
classical softmax for the output since we were in a multi-output classification setting. We trained the
network with the Adam optimize and a categorical cross-entropy loss for 30 epochs.

We present the accuracy and loss of the model in the next figure, on both its train set and the remaining
validation set.
<img width="296" alt="Accuracy with valid" src="https://user-images.githubusercontent.com/64918024/149038590-917e7123-8d7d-4380-bfcb-cc5db867d78d.PNG">
<img width="294" alt="Loss with valid" src="https://user-images.githubusercontent.com/64918024/149038607-972a2584-6203-483f-a37e-dc7ae28fe59e.PNG">

We obtained 84.8% on the full test data.

