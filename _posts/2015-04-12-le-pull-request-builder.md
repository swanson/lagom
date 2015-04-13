---
layout: post
title: The pull request builder - Get your work tested before it gets merged
categories:
- blog
---

At [Yproximité](http://www.y-proximite.fr/), even if we are a small team, we validate features before and after it gets merged.
In order to achieve that, you'll need an awesome process called "The pull request builder".

Basically, the pull request builder makes your pull request **accessible on a production isolated environement**.

## Why build a pull request ?
- I'm your customer, I want to make sure that you got me right when I wrote those "specs"
- I'm the QA (Quality people), I want to make sure that it's not already broken
- I'm the lead developper, I want to try your feature while I'm code reviewing it
- Fill any other cases there...

## What do you need ?

 * Github
 * Ansible
 * Any project, in any language (PHP/Symfony2 for us)
 * [Tower](http://www.ansible.com/tower), an Ansible GUI, though it's not mandatory
 * [Tower-cli](https://github.com/ansible/tower-cli), a tool to comunicate with the Tower REST API
 * A pre-production server(s) to deploy your PR on

![Pull request builder by Yproximite](/assets/images/thePullRequestBuilder.jpg)


So you can get started more easily, I've open sourced some basic code (nothing fancy there though).

After setuping the hook on Github, you'll need some script that will treat the event (pull request opened, update, or closed) :

{% gist 616a1758044ac4b54206 %}

Then, take a look at :

{% gist 5d8189fd23ebcfee12b4 %}

And from this point, Ansible plays the deploy playbook. If you need to catchup with Ansible and if you're French, you can take a look at [my Ansible presentation]({% post_url 2014-12-3-presentation-ansible-provisionning-deploiement-applicatif %}).


Within 20 minutes (or 5 minutes if it's a pull request update), the team will recieve a notification in order to access the dedicated environment

We can push this even further and play with docker containers and deploy the pull request on a temporary VM such as RunAbove or Amazon EC2.
