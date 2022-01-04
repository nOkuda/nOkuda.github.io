+++
title = "Student Participation Tracker"
date = 2021-09-01

[taxonomies]
tags = ["rust", "sql", "software development"]
+++

Teaching is not an easy task, and keeping track of student participation while teaching can be difficult.
I decided to apply my software development skills and become more familiar with SQL to help me keep track of student participation for a class I taught during the Fall 2021 semester.
<!-- more -->

### Problem

While pondering how I could determine whether a student understood the material I was teaching,
it occurred to me that the best way to do so is to directly ask the student to demonstrate understanding.
I wasn't interested in traditional forms of evaluation, like tests and quizzes in class.
As part of an alternative evaluation strategy, I decided that calling on students in class would be useful.
This seemed possible, since my class consisted of about a dozen students.
I also decided that calling on students in a random order would give them ample opportunity to demonstrate their understanding.

Calling on students in random order isn't too difficult to do manually with slips of paper and a container.
But I wanted a more streamlined system to record student participation and to update their grades conveniently.
And so I dreamed up a software application to do the following:

- help me choose a student to call on
- record a category for which the student was being called on
- keep track of points the student had earned
- allow for making up points a student had missed in case of excused absence
- export the data in a format that I could import into the gradebook application

### Solution

My solution is `participation-tracker` (<https://github.com/nOkuda/participation-tracker>), a Rust application with a text user interface.
(The version I've actually been working with and using is stored in a private repository, just in case I've accidentally leaked student data into a commit.)
The project structure follows the standard produced by `cargo`.

#### Code Architecture

The code is architected with three major interfaces: with the user, with the database, and with the gradebook.
The user interface (code in `src/front.rs`) uses the `cursive` library to display information and options in the terminal.
The database interface (code in `src/back.rs`) interacts with the PostgreSQL database where student information is stored.
The gradebook interface (code in `src/gate.rs`) deals with the .tsv files that the gradebook application I was using could generate and ingest.

The application starts in `src/main.rs`, where command line parameters are read in order to determine what to do.
If a gradebook file is given, the application will update the database with new students on the roster and set students no longer on the roster to a "dropped" status.
It then loads the user interface, which allows for doing one of the following:

- record student participation
- export student points
- update student points
- exit the program

Most of the time, I'm using the application to record student participation.
In this mode, there are four columns.
The first column allows for selection of a student.
If the input is empty and I hit "Enter", the field will be randomly populated with a student's name.
Otherwise, if I want to select a particular student, I can begin typing a name and then press "Tab" to autocomplete it (thanks to the fuzzy matching algorithm of `fuzzy-matcher`).
The second column allows for selection of a category for which the student is being called.
The third column allows for indicating whether the student's participation was satisfactory or not.
A final column allows for saving the information input into the previous three columns into the database.

When exporting student points, the application queries the database and saves out a .tsv file of the total points each student has earned within three pre-defined periods of time
(the periods of time were separated based on exam days).
The .tsv file was formatted so that I could upload the file to the gradebook application, which would automatically update student participation scores.

Updating student points allowed for selecting a particular date and seeing all of the times a particular student was called on that day.
This was helpful when a student had missed class for an excusable reason and wanted to make up participation points.
Since the application would choose students at random even when they were absent, I recorded absent student participation as unsatisfactory and moved on to another student.
When a student then wished to make up participation points from an absence, I could look up how many times the student had been selected on that day of absence and provide opportunities to demonstrate understanding based on the categories recorded in each entry.
Once the student had demonstrated understanding, I could update that entry to have a satisfactory status, thereby increasing the participation points belonging to that student.

#### Database Schema

The database includes the following tables:

* students
* statuses
* history
* categories

The students table contains information about students in the class.
It acts as the class roster and provides the unique identifiers other tables use as foreign keys to associate data with specific students.

The statuses table indicates what status a student is in (active, dropped, etc.)
This information would be used when generating the gradebook entries so that only active students would be included in the report.

The history table keeps track of which students have been chosen so far, when they were chosen, what they were chosen for, and whether they earned a point.
This is the table that contains individual events from which student participation points can be calculated.

The categories table indicates what category a point was earned for (homework answer, question, class participation, etc.)

Further details can be found in the README of the code repository: <https://github.com/nOkuda/participation-tracker>.

### Retrospective

This was the first project in which I worked with PostgreSQL.
The experience was generally pleasant.
I found the PostgreSQL documentation easy to read and understand.
Any time I had a question about how to write a particular query, simply reading the manual answered my questions.

This was also the first time that I had to alter tables in a SQL database.
I had made a mistake earlier in the way I had stored student information.
This caused automatic gradebook updates to fail.
In order to provide the correct information for gradebook ingestion, I needed to add a column to the students database.
Again, the PostgreSQL manual was clearly written, and I had minimal struggles in getting the database to do what I needed it to do.

Another first: working with Rust's `Arc` structs.
I had started writing the code so that functions took references to objects,
but that quickly led to compiler errors when I attempted to access these objects in `cursive`,
which relies heavily on closures.
Since closures can outlive the functions in which they are defined, the Rust compiler complained that the object references may not be valid by the time the closure was called.
Maybe if I knew more Rust, I could have found a better way to assure the compiler that the objects would be alive at the time of the closure calls.
But the beginning of the semester was fast approaching, and I needed the program to work, so I used `Arc`s.

One thing I might change in the future are the hardcoded dates used for aggregating participation points by period.
A configuration file approach would likely be more amenable to the flexibility needed when the program is run for a new semester, for example.
