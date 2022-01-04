+++
title = "Car Doors, Random Sampling, and Profit"
date = 2021-10-01

[taxonomies]
tags = ["data science", "python", "machine learning"]
+++

In the manufacturing industry, money comes from maximizing the production of good parts and limiting the production of bad parts.
Can data be used to learn how to make more good parts and fewer bad parts?
As a data science intern at EWI, I helped develop and implement a Bayesian model for car door manufacturing during the summer of 2021.
<!-- more -->

I also wrote a paper about this work, which became an open access and peer-reviewed publication: [Profit-Driven Methodology for Servo Press Motion Selection under Material Variability](https://www.mdpi.com/2076-3417/11/20/9530).
This page provides a high-level overview of the work behind that paper.

### Business Problem

The business objective was to make the most money.
Making more good parts means making more money.
On the other hand, making bad parts costs money.
So making the most money is a matter of making the most number of good parts and the least number of bad parts.

Mathematically we can represent this relationship as:

$$\text{Profit} = 3 g - 2 b$$

where \\(g\\) is the number of good parts produced
and \\(b\\) is the number of bad parts produced.
We assumed that each good part had a value of 3 USD and each bad part cost 2 USD.
This mathematical formulation shows that profit increases as we make more good parts but decreases as we make more bad parts.

### Data Collection

When manufacturing a car door, a piece of sheet metal gets formed into shape by a press.
First, the sheet gets placed on a bottom component.
Then, a top component comes down on top of the sheet, pressing it into the shape it's supposed to be.
Essentially, the press is hitting the sheet so that it bends into the correct shape.

Variability creeps into this process through (1) the material properties of the input sheet metal and (2) the type of motion utilized by the press to form the sheet metal.
As an example of a material property, stiff metal needs a lot more force to bend correctly.
Not enough force results in a part that isn't shaped correctly.
However, exerting more force on the metal increases the likelihood for breaking the part.
As for motion, think of the options available for how the bottom and top components interact in relation to each other.
The bottom component might give a little or stay rigidly in place.
The top component might come down quickly or slowly.

Our metal forming process expert had already collected data for car door manufacturing from an earlier project.
At the time, he was investigating which press motion parameters tended to yield good parts from a process engineering perspective.
The data contained material property measurements for the sheet metal, the press motion parameters, and the outcome.
The original data was nicely cleaned for me by the time I was working on the project.

### Model Selection

My team decided that it would be best to build a predictive model that could tell us how likely it would be for a part to turn out good, based on the material properties of the input sheet metal and the press motion.
We chose to simplify the outcome observations to two cases only (good or bad), so we wanted a binary classifier.
Finally, since my team had been recently studying probabilistic graphical modeling, we decided to implement Bayesian Logistic Regression.
The Bayesian formulation also allowed us to see how uncertain the model was about its prediction of the likelihood of a good outcome.

### Insights

Once we had trained our model,
we could apply expected utility theory to see how many good pieces we could expect to get out of each motion for a given input.
We ran a simulation 1000 times to calculate expected returns for a few different strategies.

Surprisingly, choosing to always run the a priori best motion took second place to a more sophisticated strategy.
The winning strategy would halt production if no motion was likely to yield good parts.
In other words, the winning strategy refused to produce parts if it expected that it would produce too many bad parts.
Furthermore, the winning strategy sometimes chose to produce parts using a motion with a lower rate of success.
While this may seem counterintuitive at first, this makes sense when realizing that the a priori best motion took longer to execute than the other motions.
This meant that expected utility theory could tell us when it would be more profitable to produce more parts with a lower rate of success than fewer parts with a higher rate of success.

Further details on what we did and what we learned is available in our open access and peer-reviewed publication, [Profit-Driven Methodology for Servo Press Motion Selection under Material Variability](https://www.mdpi.com/2076-3417/11/20/9530).

### Retrospective

It was fun applying my Bayesian statistics and Monte Carlo sampling knowledge in a business setting.
I had studied these topics as a computer science graduate student, and I got to make use of them outside of the classroom.
I implemented the model in Python using the PyMC3 library on a Windows machine.
The work was saved to a private GitHub repository owned by the company.

One detail I learned through this project is the difficulty of properly sampling from a statistical model that includes a latent categorical distribution.
One iteration of our model included a latent categorical distribution to account for the different motion types.
In general, latent categorical distributions cause the posterior distributions to take on a funnel shape, which was very hard for the sampling technology to sample from.
One solution to this sampling problem is to marginalize out the latent categorical distribution.
Since my team was interested in differentiating performance between motion types, this solution was not acceptable.
Instead, we trained one Bayesian Logistic Regression for each motion type, training each one using only the data from the corresponding motion type.

Of course, this work wouldn't have been possible without the support of many people,
especially those listed as authors on the paper.
The publication itself also required generous support (both editorial as well as financial) from key staff within EWI.
