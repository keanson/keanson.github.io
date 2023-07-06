---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

{% if author.googlescholar %}
  You can also find my articles on <u><a href="{{author.googlescholar}}">my Google Scholar profile</a>.</u>
{% endif %}

{% include base_path %}

{% include base_path %}
<hr width="80%">
{% for post in site.publications reversed %}
  {% include archive-pub.html %}
{% endfor %}

### CLIP as Multi-Task Multi-Kernel Learning

Y. Lin*, **T. Ke***, X. Xia, J. Yin, J. Xu, T. Cai, J. Lu

Submitted, 2023.

### Revisiting Logistic-softmax Likelihood in Bayesian Meta-Learning for Few-Shot Classification
**T. Ke***, H. Cao*, Z. Ling, F. Zhou

Submitted, 2023.


  
