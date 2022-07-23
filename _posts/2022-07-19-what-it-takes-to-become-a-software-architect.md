---
layout     : post
author     : Alan Tai
date       : 2022-07-19
title      : What it takes to become a Software Architect
image      : posts/2022/07/19/cover.jpg
categories : Architecture
---
I had many conversations with software engineers in various domains and disciplines during the past 20 years of my career as a software engineer and software architect. Some of them were senior engineers having 8 or 10 years of experience. Many of them were early in their career having 3 to 5 years of experience. Some of them were my colleagues. Some were job candidates. At some point, they all ended up with the same question that I heard over and over again:

> "I want to become a solution architect. What are the resources to learn more about architecture?" — A question asked by many software engineers.

Well, they were asking the wrong question. You will know why if you read on. Also, since [my company](https://www.zuehlke.com/en) is hiring [Lead Software Architects](https://1brd.ly/d6GYz?st=c16psk0dqgrh) as of this writing, it is worth explaining what a Software Architect is and how to become one.

## What is Software Architecture?

What is software architecture in the first place? As the Software Architect title implies, a common analogy that I often hear is the construction of a building. The building architect creates the blueprint of the building. The engineers build it physically and make it work according to the blueprint. I would argue that this is a poor analogy in multiple ways.

The building analogy makes people focus too much on the static aspects of the system. The creation of a city is a better analogy in this regard since it involves both static elements like roads, buildings and bridges, and dynamic elements like traffic flows and people living in the city. The architect is the person who comes up with the design of the city, the plan of how it should be built, and how everything is going to fit together. Last but not least, the architect has a vision of how the city would evolve. But software architecture is more than that. Sometimes the architect needs to zoom in and out of the city making sure that the plan would work. The architect probably won't care much about the interior design of a building while it is important to decide where to put a traffic light. An architect would need broad and, at the same time, deep technical knowledge to build software that is often as complex as a city. The architect would also need to communicate a consistent message to the business stakeholders without all the technical details to be successful.

The building construction analogy implies that the architect is the most senior person in a team who makes all the critical decisions, creates the design specifications upfront and later enforces the design during implementation. At first, it looks cool to a lot of people to become the most experienced person in the room, reaching the top of technical seniority. It might make you feel good to have the entire team listen to you, following the orders you give and the decisions you make. But when you think about it, this gives the architect so much pressure on making the right decisions, which is often not quite possible at such an early stage of a project. In an Agile team, it means that the development team are not self-organised and the architect is an obstacle preventing the team from becoming agile. The architect takes all the ownership away from the team. Who would like to be in such a position under high pressure?

### Role of Software Architecture

Software architecture can be seen as the centrepiece sitting between the business goals and the software system that supports the business to meet these goals.

![Role of Software Architecture](/assets/img/posts/2022/07/19/architecture.png)

Software architecture is used to satisfy business goals. It is designed based on the business goals to be achieved. A software system is then being implemented according to the design and it should conform to such design. This should be an iterative process. As I mentioned earlier, it is often not possible to make the right decisions at the early stage of a project. The best decision is the decision you don’t have to make, yet. An architect would create just enough design for implementation to satisfy business needs. This closes the feedback loop as you might have seen something similar in Agile and DevOps practices. The idea here is that as the world changes, so do the business requirements. The software architecture should support the business to adopt changes by evolving with the requirements.

### Software Architecture "-ilities"

Software architecture is such a challenging topic that you have to design a system that satisfies all the business requirements and all the quality attributes at the same time. This would be near impossible to be accomplished by just one person. Often this is the result of the collaboration of a team of architects and domain experts.

![Common software architecture quality attributes](/assets/img/posts/2022/07/19/wordcloud.png)

Software architecture is not just diagrams, boxes and lines. Let’s look at one of the quality attributes, security, as an example. I probably won’t be able to see any security issues by just looking at the high-level design diagram with some lines connecting to some boxes. I won’t even know if the design really works at all. It is the code that secures the system. Therefore, it is important that the architect writes code and works closely with the development team. Alternatively, it might also work for architects who can read code and communicates often with the development team. That’s what happens when a Software Architect zooms in and out of the design as I mentioned in the city design analogy. If an architect doesn’t talk to the development team and also doesn’t code, they decouple themselves from reality and the architecture they produce won’t be practical.

## What is a Software Architect?

Historically, Software Architects are the team who sketched logic flow specification on paper and handed it to another team, called punchers, to produce punched cards and make the software work.

![Punched cards](/assets/img/posts/2022/07/19/punchedcards.jpg)

With the advance in technologies, we no longer need such a process and the definition of Software Architects has become less well defined. In modern IT, Software Architect is not a title or a rank since they won’t give you the highest software development authority overnight. It is a role just like any other role in a Scrum team. I would argue that Software Architect is the most interesting role. Unlike doctors, civil engineers, psychologists, social workers, or accountants, you don’t earn a Software Architect qualification with a single course of training. In fact, almost none of the universities in the world offer a degree in Software Architecture. A Software Architect needs a broad, and at the same time, deep knowledge of almost everything in order to accomplish the goals mentioned in the previous section. So what does a Software Architect do with these skills and knowledge?

[![Software Architect comic](/assets/img/posts/2022/07/19/comic.gif)](https://dilbert.com/strip/2008-03-04)

### What does a Software Architect do and with whom?

As I said earlier, software architecture sits between the business goals and the software system that satisfies them. The Software Architect connects the businesses with the development teams who speak different languages, think differently, and have different concerns. With multiple layers of management and development team silos, it only makes communication worse. It is not uncommon that Software Engineers who have the expertise and hold the relevant information are not the decision-makers, and those people in the management team are the decision-makers without the relevant information. The Software Architect plays a critical role in climbing up and down the corporate ladder and zooming in and out of the technical details in order to resolve conflicting trade-offs and drive the organisation forward.

![Architecture Astronauts](/assets/img/posts/2022/07/19/astronauts.jpg)

In contrast, when people have the title of Software Architect in some organisations but they only stay at the top of the corporate ladder or they only zoom in of the picture, they decouple themselves from reality. As [Joel Spolsky](https://www.joelonsoftware.com/about-me/) said, these people are called [Architecture Astronauts](https://www.joelonsoftware.com/2001/04/21/dont-let-architecture-astronauts-scare-you/) who don’t contribute and are unproductive. Many large organisations seem to be able to afford a lot of them!

## Skills of a Software Architect and ways to acquire them

### Diverse experience

A diverse experience in just almost everything is critical to the role of a Software Architect. It lets you know when to climb up and down the corporate ladder and what to look at when you zoom in and out of the picture. You will also observe patterns when certain architectural designs are applied in different contexts. After having worked for over 10 companies, I ended up working for [Zuhlke](https://www.zuehlke.com/en), a consulting company, where I no longer needed to change jobs to gain a wide spectrum of experience. I am offered projects from an essentially unlimited range of industries including FinTech, InsurTech, logistics, manufacturing, luxury fashions, start-ups, and the list goes on.

### Domain knowledge

Deep knowledge of certain business domains is crucial to the success of a Software Architect because you have to know not only what it is, but also what will be or could be and why. The clients often come to the Software Architect and ask to show them what and how the business leaders are doing. Domain knowledge also helps Software Architects to speak a language common to the business, which in turn helps them climb up and down the corporate ladder connecting the management with the development team.

### Interpersonal skills

A Software Architect is also a great communicator. Many great Senior Software Engineers find it difficult to get promoted to Software Architect as they haven’t proved themselves with skills such as listening, verbal and written communications, facilitation, conflict management, presentation, negotiation and persuasion. The exact types of skills required for the job depend on the specific company environment you are working for.

At my company, I’m offered the opportunities to practice these skills in safe environments such as the ones we called “[Zuhlke Day](https://www.zuehlke.com/en/welcome-to-zuhlke-hong-kong)” and “[Zuhlke Camp](https://www.zuehlke.com/en/insights/zuhlkes-curiosity-capital-learning-development-during-a-pandemic)”. My employer also offered me formalised [training](https://www.zuehlke.com/en/our-projects/getting-better-together-at-zuhlke-you-never-stop-developing) in these areas. Lastly, the constructive feedback culture of the company has supported the growth of many individuals.

![Zuhlke Asia Camp 2019 @ Legacy Yen Tu, Vietnam](/assets/img/posts/2022/07/19/camp.jpg)

### Technical skills

There isn’t a single university degree that qualifies you as a Software Architect. You need to learn everything you can about all areas of software engineering including software design, coding, quality assurance, DevOps, performance engineering, software security, project management, software support, and so on. These skills are crucial to creating solutions that satisfy software architecture “-ilities”. When talking to the experts in a development team, a Software Architect understands better the relevant information because they have gained hands-on experience in these areas.

In a typical day of work as a development team member, I can work on areas of all kinds including backend, frontend, and DevOps. This gives me a first-person view of what’s going on behind the scene and enables me to keep a close distance from my teams.

### Business and development processes

Business processes describe the business operations of an organisation and define business requirements that are usually not stated clearly as software project requirements. The Software Architect is expected to know, or at least to know whom to ask for, the relevant information of the business processes. It is not uncommon to see that it takes a Software Architect years to become a domain expert by delivering solutions to organisations from one industry.

Understanding the importance of technical processes, software development lifecycle and best practices is as important as knowing business processes. This is because the Software Architect usually has a critical role in ensuring the alignment between the business and the development processes, which is required for having iterative deliverables and realistic project planning.

### Leadership

By now, you should be amazed how Software Architects have all the knowledge and mastered all these skills. Well, the answer is, they don’t! It is simply not possible for one person to have all that. It takes a team of capable experts to build great products. Successful Software Architects are often effective leaders in having great members in their team and growing them to become something bigger than individuals.

Software Architects are often seen as the representative of a team. They put great effort into leading, managing, and aligning the business side with the technical side. While people often think that leaders only lead in front, sometimes it takes all [five leadership styles](https://learn.coactive.com/your-leadership-approach) in one project. The leadership training [my company](https://www.zuehlke.com/en) offered taught just that.

*Are you ready to level up your career by becoming a Software Architect? Now is the best time to invest!*
