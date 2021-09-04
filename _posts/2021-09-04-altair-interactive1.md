---
layout: post
title: 'Discovering Altair - interactive plots'
author_profile: false
subpage_include: altair-interactive-files/notebook.md
######subpage_include: altair-interactive-files/notebook.html
tags:
  - toolbox
  - Altair
  - things I learned this week
  - opinions
date: 2021-09-04
---

Diving into data analysis subjects made me realize how little attention we pay to all kinds of bias in quantum chemistry research. Leaving a deeper exploration of this topic for the future, with this post, I would like to relate to the "cognitive bias," which inevitably enters the data analysis and data presentation stages of the research process. Some of the questions I ponder on for quite some time are: how to present research results objectively, yet discuss them (necessarily) in the context of state-of-the-art knowledge, and how to document and discuss the data we collect, including the data that does not fit to the desired narrative and does not lead to anticipated conclusions.

The way I see it, such issues are - to some extent - "byproducts" of the format we most often use to communicate our research through publications in scientific journals. These typically impose a fixed form presentation and rarely focus exclusively on the data, which (if available at all) is usually buried in the supplementary information, lack good standardized descriptions (such as the ["datasheets for datasets"](https://arxiv.org/abs/1803.09010)) or are in formats that may be hard to reuse. All that does not invite to explore the data and does not make it easy for the readers to disengage from the author's opinions. In quantum chemistry and other sciences where the presented data is "raw" or minimally processed, this may not be such a big issue as in social studies (as discussed [in this work](http://users.eecs.northwestern.edu/~jhullman/VIS17_Expectations_SocialVis.pdf)), yet I believe it is something to keep in mind. 

Reading scientific publications requires a lot of expertise, critical thinking, and awareness that the author(s) of the publication had a specific objective to reach (e.g., publication in this journal) and a certain message to convey. This, I think, is one of the reasons for which entering a new research field can be so difficult - without the training and domain knowledge, one is more prone to be hooked by a catchy title or abstract and fall into a marketed story. Without the tools to actively read the research papers, it is also not easy to fully appreciate their contribution.

One way to reduce this bias and make the research more transparent and inviting is to create frameworks that allow the reader to easily interact with data. Such a framework could be considered a complement to the (then - more opinionated) scientific publication. It could also be an exciting option for the automatic summarization of research projects (as discussed [recently](https://arxiv.org/abs/2012.07619)), a way to ignite more scientific discussions and a more informed alternative to popular TOC abstracts, whose role is only to catch attention. In practice, this can be easily realized in computational notebooks, in environments offered by [Jupyter](https://jupyter.org/), [RStudio](https://www.rstudio.com/), [Observable](https://observablehq.com/) or many more, recently praised [in an article in Nature](https://www.nature.com/articles/d41586-021-01174-w).

Whether - as authors - we should strive for objectivity in the presentation of our research work is another question. Yet, I believe specifying and separating the roles and motivations for using various communication mediums - for instance, allowing a more subjective discussion in scientific publications while providing interactive platforms to enable others to draw their own conclusions - could set the expectations right. I also feel that this could trigger more empathetic, inclusive, and creative behaviors in research work - it would, for instance, address the fact that we all have different ways to learn, as the authors of the [The "Paper" of the Future](https://www.authorea.com/users/23/articles/8762-the-paper-of-the-future?commit=d4033594de841d252b3220927b39de4314d26409) convey.

This is already a rather long introduction to a small technical post, yet I hope to have motivated the exercise I want to dive into. Specifically, I would like to show how to prepare a Jupyter dashboard that assembles interactive plots designed with [Altair](https://altair-viz.github.io/index.html). The data used in this example is taken from our recently submitted [paper](https://onlinelibrary.wiley.com/doi/10.1002/qua.26789).


 





This post can also be viewed as a jupyter notebook - [link](link to nbviewer).


{% include_relative {{page.subpage_include}} %}


I am still not entirely convinced of Altair (or maybe just got used to matplotlib+seaborn), but it has many exciting features that help to create interactive presentations. Also, perhaps the example above is not the best to advocate for the interactive plots. Since the data is small, these plots do not necessarily make up for better communication than the static graphs and tables. Yet, I hope it shows another possibility to present research and that it brings some more fun into it.






