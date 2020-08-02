# Pluralistic Approach to Decoding Motor Activity from Different Regions of the Rodent Brain
### By Pod-96-manymanifolds

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/neurorishika/pod-96-manymanifolds/master)
[![made-with-python](https://img.shields.io/badge/Made%20with-Python-1f425f.svg)](https://www.python.org/)
[![GitHub license](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)](https://github.com/Naereen/StrapDown.js/blob/master/LICENSE)
[![Open Source Love svg1](https://badges.frapsoft.com/os/v1/open-source.svg?v=103)](https://github.com/ellerbrock/open-source-badges/)
[![Awesome Badges](https://img.shields.io/badge/badges-awesome-green.svg)](https://github.com/Naereen/badges)

![Project Landing Image](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/pod-96-manimanifolds.png?raw=true)

#### Project Contributors:
[Narotam Singh](https://scholar.google.com/citations?user=ykkiUvcAAAAJ&hl=en) ([@narotamsingh](https://github.com/narotamsingh))  
Prakriti Nayak ([@PrakritiNayak](https://github.com/PrakritiNayak))  
[Rishika Mohanta](https://orcid.org/0000-0002-1396-3215) ([@neurorishika](https://github.com/neurorishika))  
[Vaibhav Sharma](https://www.linkedin.com/in/vaibhavsharma08/) ([@VaibhavSharma08](https://github.com/VaibhavSharma08))  

## Scientific Question

Can neuronal spikes and population activity in different motor implicated regions of the rodent brain predict the motor output and directional motor output accurately?

## Introduction

There exists a high degree of correlation and redundancy in the activity of neural populations across different regions of the brain. Motor function is a result of multiple activities coordinating over various regions, governing from the decision to carry the output to the performance of the same. In this project, we explored different approaches to decode activity from motor implicated regions in rodents using spike and population activity data.

## Methods

### Original Experiment Description
Read about the details of the experiment at https://doi.org/10.1038/s41586-019-1787-x. We are concerned with the motor activity (motion of the wheel) during the duration when the visual stimulus is not correlated with motion of the wheel to avoid confounders.

### Data source
The original data is from "Distributed coding of choice, action and engagement across the mouse brain." Steinmetz et al. (2019). The original raw data is available at https://figshare.com/articles/steinmetz/9598406. We use a precleaned version provided for us by Neuromatch Academy available under the Open Science Framework (OSF) at https://osf.io/agvxh (part 1), https://osf.io/uv3mw (part 2), and https://osf.io/ehmw2 (part 3). 

We then further cleaned to consider only recordings from motor related areas with more than 50 neurons from atleast 2 mice (figures below). We only considered the the open loop condition ie. data between stimulus onset and go cue to avoid representations of moving stimulus from appearing in the neural data we are analysing. 

![Motor Control Schematic](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/motor_control_schematic.png?raw=true)
#### Fig: A Schematic of the Motor Control System with regions involved and associated regions in Steinmetz et. al. 2019 Dataset

![Data Summary](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/data_summary.png?raw=true)
#### Fig: Summary of the Cleaned Data

### A Pluralistic Strategy
Our mentor [@JasonRitt](https://github.com/brownritt) made a really inspiring statement which amounted to something like "Sometimes, there is no one best way to do something. Find a reasonably good way to go ahead, and acknowledge the limitations." We took it to heart and decided to take a pluralistic approach to answering our question.

![Pluralistic Approach](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/pluralistic_approach.png?raw=true)

### Population Spike Code Approach
We implemented a General Linear Model with Spiking History and L2 regularization pipeline in ``python 3.6`` to decode the motor output (``wheel``) from the neural spike data from 50 randomly sampled neurons from 100 randomly sampled trials from different (Session, Brain Area) pairs. We implemented this using Cross Validated Ridge Regressor in the ``scikit-learn`` package.  The length of the temporal kernel ie. the spiking history required for the decoding models to decode optimally can vary from region to region. So we evaluate different kernel sizes between 50 to 250ms and choose the optimal kernel size that maximizes R^2 score for analysis.

### Latent Information Approach

We implemented a pipeline to compute latent dimensions of the neural spike data from 50 randomly sampled neurons from 100 randomly sampled trials using the Gaussian Process Factor Analysis (Yu, 2009) implemention in the ELEctroPHysiology ANalysis Toolbox (``Elephant`` package in ``python 3.6``). This was followed by a Ridge Regression to reconstruct motor output (``wheel``) and Linear Discriminant Analysis (LDA) to classify left motor output, right motor output and no motor output.

## Results

### Dataset for Left-Right motion is not completely balanced.

![Output Balance](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/motor_output_balance.png?raw=true)
#### Fig: Within trial distribution of motor output (``wheel`` velocity) in our duration of interest

We found that our data set is not very balanced in terms of motor putput. For a majority of the trial open loop duration the animal is not moving and is stationary. We have to account for this in our analysis. 

##### Spikes from brain areas roughly predict Motor Output to different degrees
![GLM Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/spikecode_illustration.png?raw=true)

In our analysis of the Spike Trains using our time-resolved L2 regularised Linear Model based decoding model, we evaluated the model R^2 score and prediction-ground truth Pearson's correlation coefficient for 20 80%-20% train-test splits. 
![GLM Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/regression_metrics_GLM.png?raw=true)
#### Fig: Time-Resolved Linear Regression Goodness-of-Fit Analysis
We find that not all regions of the brain are equally good at predicting the motor output. Certain motor implicated regions can predict motion much better than others such as the primary motor cortex. Interstingly, regions associated with motor feedback such as the somatosensory cortex could also predict motor output effectively, while other regions such as mediodorsal nucleus of thalamus fails to predict it accurate.
![GLM Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/rvsr2_GLM.png?raw=true)
#### Fig: Corelation between Goodness-of-Fit measures
Futher, the two regression measures we used were highly correlated.

![GLM Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/r2_sensitivity-GLM.png?raw=true)
#### Fig: Senstivity of R^2 Score to Session ID
Even within the same regions there is huge variability in the ability to predict the motor output. This is true not only across mice and sessions but also within multiple samples from the same session.

![GLM Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/d_GLM.png?raw=true)
#### Fig: Optimal Length of Temporal History required for predicting motor output
The optimal temporal history required to predict the motor output highly varied in our constrained range of 10-250 ms suggesting different timescales of motor activity in the brain. 

![GLM Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/d_sensitivity-GLM.png?raw=true)
#### Fig: Sensitivity of length of temporal history to Session ID
The optimal temporal history itself has sme sensitivity to Session ID

### Amount of Motor Information coded in the brain is different across Brain Regions
Here, we try to quantify the information stored in the coefficients of the Linear Model coefficients. We look at the temporal coefficients for the 50 neurons and quantify the amount of information in the distribution of coefficients. If there is a diverity of kernels, it may suggest that different neurons are responsible for different properties of the motor output. We quantify the information in kernels by considering different metrics for information such as average entropy of coefficient distribution over time, average variability in coefficients over time, and fraction of PCs required to explain 90% variability.

### Latent Neural modes of different areas don’t separate Motor output Equally
![GFPA-LDA Results](https://github.com/neurorishika/pod-96-manymanifolds/blob/master/Documentation/latentcode_illustration.png?raw=true)

The details to be updated soon.

#### References

* Steinmetz, Nicholas A., Peter Zatka-Haas, Matteo Carandini, and Kenneth D. Harris. "Distributed coding of choice, action and engagement across the mouse brain." Nature 576, no. 7786 (2019): 266-273.
* Shenoy, Krishna V., Maneesh Sahani, and Mark M. Churchland. "Cortical control of arm movements: a dynamical systems perspective." Annual review of neuroscience 36 (2013).
* Gallego, Juan A., Matthew G. Perich, Lee E. Miller, and Sara A. Solla. "Neural manifolds for the control of movement." Neuron 94, no. 5 (2017): 978-984.
* Byron, M. Yu, John P. Cunningham, Gopal Santhanam, Stephen I. Ryu, Krishna V. Shenoy, and Maneesh Sahani. "Gaussian-process factor analysis for low-dimensional single-trial analysis of neural population activity." In Advances in neural information processing systems, pp. 1881-1888. 2009.
* Yegenoglu, Alper, Detlef Holstein, Long Duc Phan, Michael Denker, Andrew Davison, and Sonja Grün. Elephant–open-source tool for the analysis of electrophysiological data sets. No. FZJ-2015-06042. Computational and Systems Neuroscience, 2015.
* Pedregosa, Fabian, Gaël Varoquaux, Alexandre Gramfort, Vincent Michel, Bertrand Thirion, Olivier Grisel, Mathieu Blondel et al. "Scikit-learn: Machine learning in Python." the Journal of machine Learning research 12 (2011): 2825-2830.
* Waskom, M. "seaborn: statistical data visualization. Python 2.7 and 3.5."

#### Acknowledgements

The project members express their gratitude to the entire NMA team for making and conducting the Neuromatch Academy 2020. A special thanks to our Mentor [Jason Ritt](https://vivo.brown.edu/display/jritt). And last but not the least our TA [Shagun Ajmera](https://scholar.google.com/citations?user=Kr9Y0eMAAAAJ&hl=en). We also like to acknowledge the beautiful cartoons made by [@zekeart_](https://www.instagram.com/zekeart_/?hl=el) which we edited for our illustrations.


This project was done as part of [Neuromatch Academy](https://www.neuromatchacademy.org) July 13-31 2020
+
Project Code and Data Archive under MIT Licence. 2020
