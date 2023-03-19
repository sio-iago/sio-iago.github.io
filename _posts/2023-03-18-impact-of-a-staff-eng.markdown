---
layout: post
title: "Leadership: Senior Engineer vs Staff Engineer"
date: 2023-03-19 00:00:00 -0300
categories: staff-engineer career leadership
---

Back when I was starting my career as a Software Engineer I didn't even know about 
what career ladders where possible or titles that existed in the market. Today, as
a Staff Engineer, I can't point out exactly when I became a Staff Engineer or what
was I doing different from a Senior Engineer. There are many overlaps and a lot of
work that's about the same both as Senior and Staff Engineer, but one of the 
differences lie on the scope of work and impact: Team impact versus Organization
impact.

Let's break it down with a fun example:

Imagine there's a Sr. Software Engineer called John. He works in an e-commerce
startup and his team is responsible for the product page. John became a domain
expert in all things related to products, knowing all applications and features
the team owns. He's been mentoring a few Engineers on his team so, one day, like
him, they will become Senior Engineers. Every time there's a new feature request,
integration with other teams or incidents in production he's the one everyone
calls.

Just last week he was involved in a cross team effort to implement a recommendation
service that will display similar products inside the product page. He's been
collaborating with many stakeholders to make sure he and his team can deliver all
the features required to launch this new recommendation feature.

After weeks into the project an incident occurs: some product images are not
being displayed correctly. He knows the image caching system is up, so is the
image storage and the product image service. Nothing seemed wrong, yet, the
images were intermittently not showing. He then stops working on the recommendation
work to try to debug and fix the production issue.

> Here's the first difference between a Senior and a Staff Engineer: Staff Engineers
> train and trust people on their team to fix issues. A Staff Eng develops people
> to make sure nothing stops if he/she is not around, which will be very frequently.

John manages to fix the issue in about two days. On the day after he gets a call from
a Staff Engineer that's been working with him to integrate the product service with
the recommendation service. The Staff Engineer enquires John on reliability issues
the integration may cause. He's concerned the product service won't be able to
handle the big volume of calls for product information coming from the new system,
since the service's cache is very limited: it only caches information of the 100
most popular items on the last hour. Also, the database seems to be small for
the volume of calls the Staff is expecting. The application also seems to have a
few performance issues on the APIs that will be called.

John gets very impressed with the Staff Engineer: he's never worked on any of the
product services but still managed to know implementation details and limits on
the applications.

> The second difference: a Staff Engineer is not operating on a team level but
> across many teams on the organization. A Staff possibly knows about many
> different systems and their dependencies (or does the research when requirements
> need the knowledge of various systems).

After identifying the performance concerns, the Staff recommends that John should
write the requirements for the team, so they can address the issues. He asks John
to participate in a meeting with other Engineers involved on the recommendation
project, so he can have a voice on architecture and design decisions.

> A Senior Engineer is a **leader**. A Staff Engineer is a **leader of leaders**, which
> has a lot of technical mastery but also leadership skills to insipire and include
> others on participating in important decisions. Besides empowering coworkers,
> this relates to the first difference: each team leader should know exactly what
> needs to be done, so the workstream continues even when the Staff is not around.

John is very happy to be included in all the important discussions on the project.
He sees the Staff asking questions, giving suggestions, but also answering questions,
sharing business knowledge related to the project and trusting the engineers on that
meeting on architecture and design decisions.

A few months went by and the project was delivered successfully. The Staff presented
the numbers and gave kudos to everyone involved. John was stunned by the amount of
people and different business areas involved in the project. His manager told him
that there were over 10 different teams involved and more than 30 people working to
deliver this very successful project. John then decides he wants to lead and deliver
a project like this one day.

> Final difference: While Senior Engineers lead their teams to deliver the features
> for the project, Staff Engineers lead teams through team leader to deliver company
> wide projects that impact many teams.

There's a lot more to being a Staff Engineer, but for this article I wanted to touch
specifically on the leadership aspects of it, since no one taught me them before I 
became a Staff Engineer.

/iago