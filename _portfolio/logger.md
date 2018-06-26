---
layout: portfolio
title: "RoboCup Logger App"
blurb: "This app connects to an Aldebaran Nao robot and streams data from the robots' two cameras to an iPhone. The app decodes bitmap data to save and display the camera's view in real-time."
platform: "iOS"
work: "Design and implementation"
link: ""
when: "2016"
order: 3
---

<video src="/img/portfolio/logger/demo.mov" autoplay poster="2.png" class="portfolio-image-right"></video>

Bowdoin's RoboCup team is a group of computer scientists who program robots to play soccer autonomously in international competitions. These robots have two cameras on their head that allow them to see their surroundings. From there, they use advanced computer vision algorithms to detect soccer balls, landmarks on the soccer field, and other robots.

Most of the debugging data the team collects come from these cameras. Previously, collecting camera data involved using a clunky Java program on a laptop computer, the same program that was responsible for analyzing the data. Collecting images was a slow process, especially for one person.

This app streamlines the process. It quickly connects you to a robot and immediately starts streaming camera data to your phone, manipulating the binary stream to create images. You can save the incoming binary stream to your phone in our file format designed in-house, and upload sets of images to our server for further testing.

<img src="/img/portfolio/logger/1.jpg" class="portfolio-image">