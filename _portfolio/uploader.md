---
layout: portfolio
title: "Image Uploading Tool"
blurb: "Automating the process of emailing, editing, and distributing photos among Bowdoin Orient staff members."
platform: "Web/Linux"
work: "Design and implementation"
link: ""
when: "2017"
order: 1
---

A web app built for the Bowdoin Orient photo staff to help upload and edit photos to prepare them for print and web publishing. A drag-and-drop interface lets photographers upload images to the server, and another form lets them provide valuable information about the photos, allowing editors to easily caption the photos. A process running on the server detects new photo sets, converts them into the correct sizes and color spaces required for publishing, uploads them to an S3 bucket, and sends a link to a specific Slack channel so photo editors can download and distribute the photos on production night.