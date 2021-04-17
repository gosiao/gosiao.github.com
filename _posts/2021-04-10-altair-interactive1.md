---
layout: post
title: 'Discovering Altair - interactive plots'
author_profile: false
subpage_include: altair-interactive-files/notebook.md
tags:
  - toolbox
  - Altair
  - things I learned this week
date: 2021-04-10
---

Diving into data analysis subjects made me realize how little attention we pay to all kinds of bias in quantum chemistry research. Leaving a deeper exploration of this topic for the future, with this post I would like to relate to the "cognitive bias" which inevitably enters the data analysis and data presentation stages of the research process. Some of the questions that are with me for quite some time is how to present research results objectively, yet discuss them (necessarily) in the context of state-of-the-art knowledge, and how to document and discuss the data we collect, including the data that does not "fit" to the desired narrative and does not lead to anticipated conclusions.

The way I see it, such issues are - to some extent - "byproducts" of the format we most often use to communicate our research, which is through publications in scientific journals. These typically impose a fixed form presentation and rarely focus exclusively on the data, which (if available at all) is usually buried in the supplementary information, lack good standardized descriptions (such as the ["datasheets for datasets"](https://arxiv.org/abs/1803.09010)) or are in formats that may be hard to reuse. All that does not invite to explore the data and does not make it easy for the readers to disengage from the opinions of the author(s). In quantum chemistry and other sciences where the presented data is "raw" or has been processed to a little extent, this may not be such a big issue as in social studies (as discussed [in this work](http://users.eecs.northwestern.edu/~jhullman/VIS17_Expectations_SocialVis.pdf)), yet I believe it is something to keep in mind. 

Reading scientific publications requires therefore a lot of expertise, critical thinking and awareness that the author(s) of the publication had a certain objective to reach (e.g. publication in this journal) and a certain message to convey. This is one of the reasons for which entering a new research field can be so difficult - without the training and domain knowledge, one is more prone to be hooked by a catchy title or abstract and fall into a marketed story. Without the tools to actively read the research papers, it is also not easy to fully appreciate their contribution.

I think that one way to reduce this bias and make the research more transparent is to create frameworks that would allow the reader to easily interact with data. Such a framework could be thought of as a complement to the (then - more opinionated) scientific publication. It could also be an interesting option for the automatic summarization of research projects (as discussed [recently](https://arxiv.org/abs/2012.07619), a way to ignite more scientific discussions and a more informed alternative to popular TOC abstracts, whose role is only to catch attention.
 
Whether as authors we should strive for objectivity in the presentation of our research work is another question, yet I believe that specifying and separating the roles and motivations for using various communication mediums - for instance, allowing a more subjective discussion in scientific publications, and sharing other tools, such as interactive platforms, to allow others to explore and use the data - could set the expectations right.

This is already a rather long introduction to a small technical post, yet I hope to have set the motivation for the exercise I want to dive into. Specifically, I would like to show how to prepare interactive dashboards in Jupyter, that assemble interactive plots prepared with Altair. The data used in this example is taken from [the paper that we recently submitted to IJQC](). I wonder whether this presentation makes up for better communication than the static graphs and tables within that paper. Let me know in the comments below.



{% include_relative {{page.subpage_include}} %}


