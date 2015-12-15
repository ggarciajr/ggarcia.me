---
layout: post
title: "Why methodologies fail software developers?"
excerpt: "Ideas behind the evolution of methodologies and its pros and cons."
date: 2014-05-04 17:48:11.000000000 -03:00
tags: [developer, software development, methodologies, agile]
author: ggarciajr
comments: true
---

Methodologies are defined as a systematic, theoretical analysis of the methods applied to a field of study. Developing a new methodology requires a new perspective on the methods and principles associated with a branch of knowledge.

Once you have the specifications of a skyscraper or a car or a motherboard, you should be able to build them by following a particular methodology.

For instance, if you’re making a building, you have to have specifications and architects to understand how to put the pieces together to make the building, but you also need a foreman to direct the labor, and make sure that the concrete mixer and the dump truck don’t show up on the same day if you only have room for one big vehicle on site. This is a methodology: the organization of labor.

Creating a methodology for building a car or a motherboard is different than creating a methodology for constructing software. Any piece in the software can change at any time.  This raises an important question regarding software construction methodology: when does the software Architect determine that it is time to start building floor 2 on top of floor 1 when floor 1 can change at any time?

Software development is a field where requirements are constantly changing due to changes in the market, government regulations, and many other reasons. It is clear that methodologies used to construct other products cannot be effectively applied to the construction of software in a timely or effective manner.

## Waterfall
Waterfall was the first development methodology applied to software development. This is a purely sequential process, in which progress flows from one stage to the next, like water:  it is an old, reliable method used
in manufacturing and construction. It is a highly structured approach that is not best applied to software development because:

 * It's not easy to react to changes, and...
 * Developers find problems and defects in later stages.

A practical approach to developing software cannot be founded in well-defined, immutable phases that a developer must leave behind to move to the next stage once a single product has been developed. Later stages of development may teach a team more about an earlier stage of development: because software is a mutable product, the developers can take advantage of this aspect, and ultimately deliver a better product to the client.

The likely outcome of using waterfall to build software is that you will end up with software that does not quite fulfill its purpose and is rife with defects. Either you or your client will spend a lot of money to fix all the problems after the initial work has been completed.

## RUP
Rational Unified Process (RUP) is the IBM-developed sister of the Waterfall methodology, marked by its flexibility in how it allows developers to act: a developer can start working on a new phase of the project before the other one has "officially" ended. Various stages of the development process take priority over others at different times in the development process, but developers can more readily move between the different "stages" necessary to complete a software product.

This means that a team of developers can more quickly react to changes, start testing and find the problems with the product earlier.

Here we see the first signs of a "reactive" methodology: however, this methodology’s flexibility is insufficient – a developer can look forward to new phases early, but cannot react to changing requirements or time once a phase has completed. Thus, projects that use RUP typically suffer from the same symptoms from which Waterfall projects also suffer.

##Agile
To try to solve the issues that the Waterfall and RUP methodologies present to software development, product managers and architects have developed several "Agile" methodologies.

These methodologies focus more on the client’s needs and on the system being built than strict adherence to the rules of the methodology that the development team employs to organize efforts on the project.

The driving idea behind Agile is that we should value the client and the product more than the methods used to build them: to make this clear, some prefer to call agile a "framework" than methodology.

###But Agile grew rigid…
A software development team cannot force a mutable product to fit into a rigid, immutable development methodology. Hardware development requires nearly permanent decision-making, and stable accomplishments upon which a development team can build and move forward. Software is more plastic than that, and teams must take advantage of software’s inherent mutability in order to move as quickly and as effectively as possible.

Applying a methodology that says otherwise is like building a car and a skyscraper with the same blueprint: it just doesn’t work given the end product in mind.

Things looked promising with the advent of Agile methodologies. Finally, developers planned small, but important features one at a time: developers were allowed to build and test single ideas, deploy them and start all over again.

And what did development teams do when given such a wonderful approach to software development? Teams reverted back to the ideas that drove development of hardware and software simultaneously in the past: they gave more importance to the theoretical ideas driving the Agile method rather than the goal of the Agile method, and completing the software in a timely and effective manner became a lesser priority to measuring the effectiveness of the methodology. Soon, Agile became overburdened with metrics and reports
that measured the effectiveness of the methods.

Methodologies should be used as tools to help people to build the software and not to be indiscriminately applied to the development process for the sake of following rules. The software itself is not a byproduct of the method: it is the goal of the method. These strictures on the natural development process and obfuscations of the goal at hand point to one end: people are willing to say that Agile is dead.<sup id="reference1"><a href="#footnote1">1</a></sup>

<div class="footnotes">
  <sup id="footnote1">1. <a href="http://pragdave.me/blog/2014/03/04/time-to-kill-agile/" target="_blank">PragDave</a> and <a href="http://rubiquity.com/2014/03/12/agile-is-dead-angry-developer.html" target="_blank">Richard Bishop</a> <a href="#reference1">↩</a></sup>
</div>
