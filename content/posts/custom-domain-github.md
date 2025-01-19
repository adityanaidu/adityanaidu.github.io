+++ 
draft = true
date = 2025-01-18T15:17:53-05:00
title = "Using Custom Domain for Static Website on Github Pages"
#description = "How to"
slug = ""
authors = []
tags = []
categories = []
externalLink = ""
series = []
+++

# Introduction

A short article on how to use a custom domain for a website on github.io

## Create a repo for your static website on Github
On github.com, create a repo [githubusername].github.io.

Pull this repo to your local machine and add a index.html file with some test content. And push it back to github.com. You can do this on the github webpage itself. So you don't really need git installed locally.

## Get a domain for yourself
I used AWS Route 53 since I am familiar with AWS already. On AWS go to Route 53 -> Registered Domain -> Register Domain Search. If the domain you want in not available, select from the suggested available domains. Make sure privacy is on so that your private info doesn't show up in whois queries.

## Add a CNAME to AWS Route 53
Go to AWS Route 53 -> Hosted Zones -> Click on the Hosted zone name -> Create Record. Here create a CNAME record with simple routing policy. For Record name add a www suffix to the domain name. Set the "Value/Route traffic to" to [githubusername].github.io from you github repo. A TTL of 300 secs should be reasonable for testing right now. In the long run it can be set to a much higher number i.e. 1 (or even 2) days.

## Setting in github
In your github repo create a file named CNAME containing only the CNAME you just added in Route 53. Also, make sure to enable https by default in your repo -> settings -> Pages -> Enforce HTTPS.

## Test!
On your browser go to https://<CNAME>. This should display what you added to your index.html page. Update the TTL of the CNAME record in AWS Route 53 to a bigger value.
