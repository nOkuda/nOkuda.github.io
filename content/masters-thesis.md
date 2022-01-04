+++
title = "Labeling Data for the Best Model"
date = 2017-08-01

[taxonomies]
tags = ["data science", "machine learning", "python"]
+++

Suppose that you have a large set of unlabeled text data.
Assuming you have limited time to label the data, which subset of the data should you label to get the best machine learning model?
And what order should that subset be labeled in?
These were the questions I worked on for my master's thesis, which I completed in the summer of 2017.
<!-- more -->

The thesis is [available for download](https://scholarsarchive.byu.edu/etd/6906/).
The code used to collect and analyze the data discussed in the thesis is [available on GitHub](https://github.com/nOkuda/reviewlabeljob).

### Problem

Sometimes, you have a lot of text data on hand that might give you important insights if you could organize them into categories.
Maybe you have a bunch of opinions submitted regarding a proposed policy change, and you'd like to see how supported the policy change is.
Or maybe you've been given a collection of leaked e-mail messages that may or may not have contained government secrets, and you'd like to know which government secrets were leaked.
You could meticulously label all of the documents, one by one, but that could be tedious and time-consuming.
This would be especially problematic if you had a deadline for when you needed to have drawn insights from the documents.

The strategy I explored in my master's thesis was training a supervised machine learning algorithm on a subset of the documents.
The open question for this strategy is to decide which subset of the documents should be labeled to produce the best model within an allotted time.
Furthermore, once we know which subset to label, we would like to know what order we should have humans label the documents.

### Data Selection

While my thesis works with a few data sets, the one data set that gets used throughout the thesis is a set of Amazon product reviews.
These product reviews contain (1) text data that the reviewer has written about a product and (2) a numerical rating from one to five indicating how highly the reviewer rated the product.
Each review serves as a document, and the associated rating serves as the document's true label.
This allowed me to use the data set for supervised machine learning.

In my thesis, I tried converting the text data into a few different modifications of features known as "topics", as defined by the field of topic modeling.
Topic modeling is an unsupervised technique for learning word distributions present in a set of documents.
Each topic is a particular probabilistic distribution of words, and there is an algorithm for inferring the distribution of topics in each document.

### Document Selection for Labeling

To determine which subset of the data should be labeled, I ran simulation experiments measuring the predictive \\(R^2\\) of the [supervised anchor words model](https://aclanthology.org/N15-1076/) as I expanded the set of labeled documents.
In addition to predictive \\(R^2\\), I measured how long each selection method took.
The workflow for each simulation was as follows:

* start with 20 documents in the "labeled" pile (act as if all other documents are unlabeled)
* until 300 documents are labeled:
  * train a model on the labeled documents
  * compute the next document to add to the labeled pile
  * reveal the label of the chosen document and add it to the labeled pile

The code I used to run these experiments is [available on GitHub](https://github.com/nOkuda/activetm).

Of the three document selection methods tested, random selection seemed best.
For the most part, random selection seemed to improve model performance about as efficiently or more efficiently than the other selection methods.
Moreover, random selection was clearly the fastest selection method.
This is an important point, since faster selection of next document to label means more labeled documents for an allotted amount of labeling time.

### Context Switching Effect

Knowing that labeling a random subset of documents is the most efficient way to train a supervised model, the next question to answer is what order those documents should be labeled.
To answer this question, I devised and ran a user study that measured the time humans took to label documents presented in a particular order.
In the user study, participants saw the text of an Amazon review one by one and had the task of labeling the text with the rating they thought corresponded with the text.

Unbeknownst to the participants, the Amazon reviews were in a particular order.
Using topic modeling, I organized reviews by their most prevalent topic, and the participants would label 16 documents in a row with the same most prevalent topic.
The next 16 documents would then have a different most prevalent topic.
This pattern would continue until participants had completed labeling five such 16-document groups.
We can refer to these 16-document groups as "topic groups".

Every participant's set of documents to label was randomly generated at the time of the study, and the amount of time each took to label the documents was recorded.
The hypothesis was that forcing humans to make a context switch from a group of documents with one most prevalent topic to a group of documents with a different most prevalent topic would increase labeling time of the first document of the set.

I implemented the backend code for the user study using Python and Flask.
A labmate helped me implement the frontend display for the participants in Javascript.
Our work is [available on GitHub](https://github.com/nOkuda/reviewlabeljob).

Using the Wilcoxon-Mann-Whitney test, I showed that the labeling time of the first document of each topic group took longer than labeling the seventh and later documents of the same topic group.
This suggests that context switching really does cause an increase in document labeling time.

To definitively show that reducing context switching would lead to faster document labeling times, I devised and ran a second user study.
This time, each participant received two treatments, one immediately after the other.
One treatment was to see the documents in random order.
The other treatment was to see documents by topic groups.
Which treatment came first for each participant was randomly determined.

In analyzing the data collected from this second user study, I resorted once more to the Wilcoxon-Mann-Whitney test and found that random labeling order tended to have longer labeling times than labeling by topic groups.
While paired t-test seemed like the obvious analysis to perform on this data, I found through the Shapiro-Wilk test that neither random labeling times nor topic-ordered labeling times were normally distributed, meaning that an assumption of the paired t-test was violated.
Therefore, I conducted the Wilcoxon-Mann-Whitney test in place of the paired t-test.

### Insights

Using the data I had collected, I concluded that the best way to label a set of documents is to
(1) randomly choose as large a subset as you can afford and
(2) show the documents by topic groups.
Furthermore, the data I collected demonstrated that the average labeling time of documents shown by topic group is about 13% less than the average labeling time of documents shown in random order.
Even if the models I worked with have now fallen out of favor, these conclusions about how to label your text data can be applied to any model that requires labeled documents.
So the next time you have a team of labelers and a stack of documents to label, keeping these points in mind might help you.

### Retrospective

The experience of my master's degree in computer science, culminating as it did in my thesis, has taught me the importance of trusting others.
Or rather, reflecting on the experience has since brought me to this realization.
After my committee had already approved my thesis, I was reviewing it one last time before submitting it for archiving.
That evening, I thought I saw a mistake and made a change I thought would correct it.
Later on, I discovered that my change had actually introduced an error, and so you can still find in the archived version of my thesis a division that does not make sense.
Luckily, the final value I asserted was correct; but I swapped the divisor and dividend in my explanation for how I calculated the value.
If I had trusted my committee to find and notify me of the mistake I had imagined, I would not have made the change, and my thesis would have remained correct.

Another example of the importance of trusting others came from an important lesson my advisor taught me.
I came into his office as usual to a weekly meeting with him, and we were reviewing comments he had made on the draft of my thesis.
As he gave me feedback, I zealously defended the choices I had made.
The defensive nature of my reaction was forceful enough that he stopped his feedback on my thesis for a moment and pointed out (with some exasperation, as I recall) that he was making suggestions to help improve my work.
That bit of wisdom changed my perspective.
Before, I had imagined that negative comments on my work were marks against me, tallied up to hurt me.
From that perspective, of course I would endeavor to invalidate comments meant to hurt me.
I have since come to see the importance of being more considerate of the people making comments about my work.
They are not all attacking me, and some of them might even be sharing helpful information.
I really appreciate that lesson my advisor took the time to help me learn.
