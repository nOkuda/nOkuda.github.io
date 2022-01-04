+++
title = "Software Development for CT Scanning Research"
date = 2018-08-01

[taxonomies]
tags = ["software development", "python"]
+++

How can a team write software in a way that helps them explore a research question?
As an intern at Buffalo Manufacturing Works in the summer of 2018 (this company would later officially rebrand as the Buffalo office of EWI),
I had the opportunity to help my team research new methods for industrial CT scanning.
<!-- more -->

As a result of my contributions, I had the honor of getting my name on the patent that resulted from this project ([Computed tomography for non-destructive evaluation of manufactured parts, US10969771B2](https://patents.google.com/patent/US10969771B2/en)).

## Business Motivation

CT scans are probably most familiar from the medical field, where you might stand or lay down in a contraption that moves around you to take x-ray images of your body.
Doctors can use the results of a CT scan to predict damage to your internal organs and prescribe appropriate measures to help you heal.
In an industrial setting, CT scans serve a similar function.
A manufactured part undergoes a CT scan, and a technician or engineer interprets the results to predict faults in the part.

CT scanners work by taking multiple x-ray images around a particular object.
Using some math, the images can be used to reconstruct a 3D model of the object.
The off-the-shelf solutions for industrial CT scanning used one method of 3D reconstruction that required taking many x-ray images.
Buffalo Manufacturing Works, as contracted by a certain client, investigated a different 3D reconstruction technique that required fewer x-ray images but more computing power.
The idea was that the usual method took a long time because of the number of images it needed to make a good reconstruction,
but the method we were investigating could potentially take a shorter period of time because fewer images would be required.
While the new method would take more computational time to provide a reconstruction of similar quality,
the hypothesis was that it would still take less time in total using the new method than the old.

## Software Development Practices

To help the project in investigating new methods for 3D reconstruction from CT scans,
I shared my knowledge of version control (git) and unit testing (pytest).
My knowledge of Python programming was also stronger than my co-workers',
so I got to coach them in that area as well.
Finally, I shared with them my knowledge of the process of code reviews and pull requests.

## Proud Moments

Besides helping my team build and maintain a code repository, I got to do some deeper technical work on the project.
One of my contributions was applying the image processing technique of watershedding to the 3D reconstruction algorithm.
We were having trouble reconstructing a sharp distinction between object and non-object,
and I remembered watershedding from my image processing class.
Watershedding is an algorithm that analyzes an image to find segments within it.
By applying watershedding to the x-ray images, I was able to get a sharper distinction between object and non-object in the reconstruction.

Another contribution I made was utilizing the ASTRA toolbox to run the computations on a GPU (Graphics Processing Unit).
We were already using the ASTRA toolbox to run our computations on a GPU,
but we were running into out-of-memory errors as we increased the resolution of our reconstruction.
Our mathematics expert noted that the method we were working with could conceivably be decomposed into sub-problems that would fit onto the GPU memory,
but we weren't sure how much effort it would take to implement that solution.
Near the end of my time working on this project, I dove deep into the ASTRA toolbox documentation and found code that suggested that the sub-problem decomposition had already been implemented in that library.
After a few failed attempts, I finally got ASTRA toolbox to work as I wished, demonstrating that the reconstruction that originally could only fit on the large GPU in our workstation could be solved on my laptop's GPU, which had many times less memory.

Perhaps the most prominent legacy of this work is resulting the [patent](https://patents.google.com/patent/US10969771B2/en).
My co-workers felt grateful enough for my contributions that they even included me as an author on it.

## Retrospective

In some ways, it is a miracle that I was trusted with such a large responsibility on this project.
My software engineering experience out in industry was limited to a few months as an intern at one company a few years previous.
I may have had help from other contributors in some of my past code projects, but those projects were largely individual efforts.
Also, I don't think I had ever worked on a project that brought in so much money.

I got to do another internship with the company again three years later,
and my previous co-workers excitedly showed me how they were still using the software development techniques I had taught them.
I was glad to see that (1) they appreciated what I did and (2) what I taught them was serving them well.
