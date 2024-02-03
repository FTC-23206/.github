---
layout: page
show_meta: false
header              :
  title             : Automaducks
  image_fullwidth   : "header_ducks.png"
title: "Diary of an Innovator - Progress and Event Experience"
teaser: "2024-01-24: Diary of an Innovator Team 23206 Shares Season Progress and Event Experience"
tags:
    - FTC, FIRST, Diary, Innovator
categories:
    - Diary
---
Original Post: [Diary of an Innovator Team 23206 Shares Season Progress and Event Experience](https://firsttechchallenge.blogspot.com/2024/01/diary-of-innovator-team-23206-shares.html)

### How has your team’s season been going so far? 

<img src="../../images/blog/Diary Entry 2a.png" align="middle" alt="Diary Entry 1a.png" width="300" />

Our game season has started off strong with many successful competitions and scrimmages. We have had 2 league meets, and have finished 2nd, which has put us in 5th place at the start of the upcoming interleague event. We also had many learnings along the way, including making sure to glue our linear slide internal screws, as one of them came off during a FIRST Robotics Competition sponsored scrimmage, rendering our robot unable to score on the backdrop. 

One key issue we chose to focus on is how to get better at picking up pixels without knocking them over. Our design is based on a front mounted claw and the drivers’ team view is obstructed by the robot when we go to the human player station. Our strategy relied on guidance from the human player, but it was still challenging to get it right from across the field. We’ve considered switching to an intake system, but we’ve finally decided to add a distance sensor that provides pixel alignment feedback through LEDs mounted in the back. 

We are also iterating on our autonomous strategy to maximize points and reliability. We are now making use of the AprilTags on the backdrop for alignment to overcome odometry inaccuracy. And now we’ve shifted from using TensorFlow to OpenCV color detection to identify our team prop. Before, our object recognition wasn’t very reliable even after many trainings, in comparison, our approach with color hue and vibrancy has proven to be more effective. 

Many of these challenges were difficult to understand, and so we’ve learned to add "RobotLogs” throughout our code which prints out useful information on “LogCat” that helps us with debugging our code. Another lesson we learned was making sure everything is well tested and practiced before competitions. A simple example is when our robot got tangled on the stage door during our very first league meet match. Due to space limitations, we had not practiced from the red side of the field, but now we try to practice as many different routes as possible across the different sides of the field. 

### Has your team participated in an event? And how are you preparing?

<img src="../../images/blog/Diary Entry 2b.png" align="middle" alt="Diary Entry 2a.png" width="300" />

So far, we’ve attended two league meets and scrimmages. Before each event, we’ll spend more time together polishing up autonomous, doing driving practice and also making sure we have extras of all our custom printed robot components. The scrimmages have been super valuable for us to acquire experience without the pressure of official events. We recommend every team to participate in local scrimmages if they have the chance. 

Every event we’ve been to so far has been filled with fun and growth as a team. Interacting with the local FIRST Tech Challenge teams is always gratifying and we’ve grown close with many teams. The most challenging part of any event has been when something breaks down or doesn’t go according to plan. We’ve had issues with broken printed parts and badly jammed linear slides. At the time we needed to make quick decisions to attempt to fix the issue (such as replacing claw parts) or work around it. Sometimes it required a change of strategy with an alliance partner and other times it meant taking notes and improving the design for the next event. 

Our next competition includes judging sessions and awards, so we are currently working on our engineering portfolio. We’ve divided the sections among our team members, so we can all focus on specific parts that we will present during the judging session. Over the course of our season, we’ve been using an Engineering Notebook, and it’s turned out to be a valuable resource. We’ve kept track of the tasks we’ve worked on and in what order we worked on them. We also started preparing our presentation so we can practice and make sure we can properly represent all the work we’ve done. We are hoping for a successful interleague event on Jan. 14th, and we will be thrilled if we are able to move on to the Washington State Championship!