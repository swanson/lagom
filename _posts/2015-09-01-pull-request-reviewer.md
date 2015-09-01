---
layout: post
title: The PR Review Watcher, save time when reviewing code
categories:
- blog
---

Are you tired when reviewing code on a pull request, to spend too much time on (important yet time consuming) details, such as:

 * Is the code PSR valid ?
 * Did the developer though of adding the config key in the right file ?
 * Are the SQL migrations generated ?
 * **Any recurrent requirements your applications needs developer to be reminded of ?**

<br>

We are trying to avoid those kind of situations:

![gif comment pull request PSR](/assets/images/pr.gif)

Well, good news, the [PR Review Watcher](https://github.com/Yproximite/PRReviewWatcher) bot is for you.

It can save time for your team by avoiding them to check rather insistently for common mistake and focus on code architecture, algoritmh, comprehension...

Feel free to [contribute](https://github.com/Yproximite/PRReviewWatcher) and to send PR's (the project is in Silex).

PS: The code style is just one example to illustrate the purpose of the project in a global way. Between you and me, on my team, there's a pre-commit hook to prevent dev from commiting non PSR2 valid code.
