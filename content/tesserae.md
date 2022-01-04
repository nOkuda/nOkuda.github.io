+++
title = "Tesserae"
date = 2021-05-01

[taxonomies]
tags = ["python", "sql", "software development", "api"]
+++

Tesserae is a software tool for finding intertexts between works of literature, primarily in Latin and ancient Greek.
Working on this open source project, both in paid an unpaid positions, gave me many opportunities to practice software development.
<!-- more -->

## Overview

The Tesserae project develops computational tools to find intertexts between works of literature.
For those unfamiliar with the term "intertext", it is a pair of literary passages such that one passage can cause readers to recall the other one.
The term "allusion" is probably better known, though scholars today tend to frown upon its use.

The [Tesserae website](https://tesserae.caset.buffalo.edu/) makes Tesserae's intertext finding results available to scholars who study Latin and ancient Greek literature.
The intertext finding algorithm, commonly referred to as "Tesserae search", looks for matching words between two passages and then scores that pairing of passages based on the matching words.
The rarer the matching words and the closer together the matching words are located in the two passages, the higher the score.
Further details on Tesserae's methods are explained in [Forstall et al. (2015) _Modeling the scholars: Detecting intertextuality through enhanced word-level n-gram matching_](https://academic.oup.com/dsh/article-abstract/30/4/503/480607) ([Preprint](https://tesserae.caset.buffalo.edu/blog/wp-content/uploads/2012/10/Modeling-the-Scholars-2013-11-6LLC-preprint1.pdf)).

Of significance is that this project has been directed primarily by academics in the humanities.
Because of my interest in both programming and Latin, the Tesserae Project was, in many ways, the perfect project for me to become involved with.

## Beginnings

My initial dealings with the members of the Tesserae Project began near the end of my time working on a master's degree in computer science.
I had reached out to a researcher about getting access to a data set he had, and he replied with, among other things, encouragement to look into the Tesserae Project.
He had, in fact, previously been a key member of the Tesserae Project before starting on his own path.
That initial encouragement eventually led me to join the Tesserae Project as a volunteer in August 2017 (also coinciding with when I started my PhD program in Classics at the University at Buffalo).

At that time, Tesserae was written in Perl.
The website used CGI to pass user-selected parameters to the Perl code, which then proceeded to run Tesserae search.
This would first read in previously serialized information from disk, look up matching word information, and score the paired passages.
Results were then serialized back to disk and displayed to the user in pages.

The [Perl code](https://github.com/tesserae/tesserae) ([old site](https://tesseraev3.caset.buffalo.edu/)) was adequate but lacked some nicer features.
Perhaps the most difficult code to understand was the most critical: the part that computed the results.
The main body of the code takes up several hundred lines of bookkeeping on data structures created from a completely different script, the text ingestion script.
A few subroutines had thankfully shortened the main body, but even these subroutines required an intimate knowledge of the data structures serialized by the text ingestion script.
From a software engineering perspective, there were many aspects of this code that could be improved.

In my first months as a volunteer, I was tasked with investigating options for replacing the Perl code.
An earlier discarded version of the software was available, and I was asked to see whether it could be adapted to the present needs of the project.
It was a code base utilizing the Solr text search software, with a custom Scala plugin to perform Tesserae scoring.
The website was built in Django, and Celery was used to pass information between Solr and Django.
The installation was relatively easy, thanks to a Vagrant image, and it appears that [I spent a little time working on making fixes](https://github.com/nOkuda/tesserae-ng), but I had difficulty imagining this code base to be understandable to a typical humanities graduate student.
I recommended that this version remain discarded and that a new version be built in Python to attract more contributors.

## Mentoring

Coinciding with my first months on the Tesserae project, I met other students interested in the project as well.
There was a particular group of four of them (all graduate students from the Linguistics department) with whom I met every week to discuss the project.
As, at that time, I was much stronger in coding ability than they were, I attempted to share my coding knowledge with them at those weekly meetings.
In addition to helping them learn Python, I introduced them to working with git and GitHub.
By the end of that first semester, we had cobbled together a [minimally working Python version of the software](https://github.com/nOkuda/pytesserae).
Although that code was never officially adopted by the Tesserae project, I think that all of us volunteers came away from the experience with valuable lessons.
Some of that group found that they did not want to code; others took that initial experience with Python and continued honing those skills in other projects.
I myself came away with more ideas about how I could change the structure of the Perl code.
In fact, I went on to write experimental versions of the code in [C++](https://gitlab.com/nozomu.okuda/compare-tess/-/tree/master/cpp) and then in [Javascript](https://gitlab.com/nozomu.okuda/compare-tess/-/tree/master/js) to work through ideas about how to organize the code.

At a later time, I taught an unofficial weekly Python course for humanities students.
A volunteer in the Tesserae project was interested to see what it was like coding in Python, so she organized a group of similarly interested students, scheduled a room for us to meet in, and asked me to share my knowledge.
I was happy to do so, and I enjoyed the experience.
When the professor in charge of the Tesserae project asked for my opinion on who should be the Tesserae Fellow (i.e., the graduate student in charge of the day to day operations of the project) for the next school year, I was able to recommend the volunteer who had organized the unofficial course, citing her previous involvement with the project as well as her motivation in organizing the course.
She did, in fact, become the next Tesserae Fellow and fulfilled her duties well.

I had the opportunity to mentor one more group of volunteers --- another two graduate students from Linguistics.
One was already proficient in coding, and the other learned over time; neither had worked on code collaboratively before.
I guided both through setting up development environments and becoming familiar with git and GitHub.

## Updating the Code

In 2018, the Tesserae Project won a research grant from the National Endowment for the Humanities (NEH) to make the Tesserae software easier to integrate with other digital humanities tools.
I was the one person selected from my school to be funded through this two-year grant; another graduate student from the collaborating school, likewise receiving funding, worked with me to fulfill the promises made in the grant application.
Although I was more familiar with the old code and had already drawn up some ideas for improving it, the other student was given the responsibility of architecting and implementing the new backend.
I was instead tasked with designing and developing a REST API through which other software could access Tesserae's functionality.
I spent much of my time on the project that first year of funding learning about REST and Web APIs.

As the second year began, the [Python backend code](https://github.com/tesserae/tesserae-v5) was beginning to take solid enough shape that I could begin directly calling it in the API code I had mocked.
Soon, the backend concerns became more of my responsibility as my teammate took on responsibility for the frontend.
During this time, I improved the speed of some parts of the code, particularly in the calls made to the database (MongoDB), the database queries, and the post-processing code.
This was accomplished largely through carefully planned automated tests as well as measuring results with a profiler.
I also worked with the system administrator to deploy the API and backend code.

## REST API

As mentioned above, my main responsibility was designing and implementing a REST API for Tesserae.
The main capabilities of Tesserae made available through the API are

* Tesserae's catalog of texts
* Recommendations for words to ignore in the texts (i.e., stopwords), as calculated by word frequencies
* Tesserae search as a service
* Results from a Tesserae search

For those interested in futher details, the [documentation](https://tesserae.caset.buffalo.edu/docs/api/)
and the [code](https://github.com/tesserae/apitess) are available.

The most difficult part of designing the REST API was fitting Tesserae search into a RESTful pattern.
Because Tesserae search requires a fair bit of computation, it was unrealistic to expect a response from the API server within a minute for all search queries.
My solution was to introduce an intermediate step between submitting a search request and retrieving the search results.
This was implemented by creating an identifier for each search request; that identifier would be used to retrieve the corresponding results.
If the identifier is used before the results are ready, the API responds so; otherwise, the API responds with the first page of results.

Implementing this two-step process also required implementing a simple asynchronous / multiprocessing work queue (in the style of a solution to the producer-consumer problem).
When the API receives a search request, it queries the backend to see if a search with the exact same parameters is already in the database.
If so, the API responds to the client with the identifier of the matching search already in the database.
Otherwise, the API receives a new identifier from the backend, pushes the identifier and the search request onto the queue, and responds to the client with the new identifier.
When the backend is ready to run search, it takes the first job off of the queue and performs the search requested.

## Desktop Application

One promise made in the project's grant proposal was to make the Tesserae software available as a desktop application.
This is desirable, for example, when a scholar is working with texts that are not allowed to be published yet.
In this case, the Tesserae project cannot host the texts on the public database, so it would be better for the scholar to run Tesserae search on a personal database.
In order to make this possible, I created a [wrapper for the frontend, backend, and API in electron-js](https://github.com/tesserae/electron-tesserae).

Despite my previous dislike of the language, I took this opportunity to become more familiar with Javascript.
My attitude toward Javascript softened a little as a result of finally understanding how async and Promises are related.

I also managed to package the Python code with PyInstaller so that the Python code could run on different operating systems.

There are some major discomforts on the initial use of the desktop application, given the long time it takes to download and install both MongoDB and the initial data for the database.
It was also hard to determine whether the desktop application really could run on multiple operating systems --- though I was able to verify that it worked on Ubuntu 20.04 and on Windows 10.

## Multitext

One final major contribution I made to the new Python code of the Tesserae project was implementation of what was called "multitext search".
Because Tesserae search can take a long time to complete, multitext search was developed by a previous generation of the Tesserae project to find intertexts shared across multiple works.
First, a normal Tesserae search is run between two works.
Then, the results from the two works is used to look up only the matching words in the list of other works specified in multitext search.
This takes much less time to complete than running Tesserae searches between every pairing of works specified in a list.

My [implementation of multitext search in the new backend](https://github.com/tesserae/tesserae-v5/blob/master/tesserae/utils/multitext.py) utilizes Python's built-in version of sqlite to store SQL databases for each multitext-enabled work ingested into Tesserae.
These databases contain information about where each pair of words occurs in a work.
Then, during multitext search time, my implementation queries the appropriate SQL databases for only the word pairs that occur in the results of the initial Tesserae search and collates the retrieved data.

## Retrospective

One of the biggest realizations I made in my work with Tesserae is that software development is a social activity.
Knowledge must be passed down from one generation of maintainers to another for a project to survive.
In many ways, the lack of easy access to the minds of the earlier implementers was the impetus for my initial push towards rewriting the code.
I had the pleasure of interacting with a number of earlier implementers a few times, and each time, I better understood more parts of the earlier code --- particularly in why they were written the way they were.
Still, time had made even their recollections fuzzy so that in some ways, I knew the old code better than they did.
