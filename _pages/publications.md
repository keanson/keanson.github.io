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

<!-- {% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %} -->

<table class="frontpage-paper-container"> 
  <tbody> 
    <tr> 
      <td> 
      </td> 
      <td>
        <span class="paper-title"> CLIP as Multi-Task Multi-Kernel Learning
        </span><br/>
        <span class="author"> Y. Lin*, T. Ke*, X. Xia, J. Yin, J. Xu, T. Cai, J. Lu
        </span> <br/> 
        <span class="venue"> Submitted, 2023.
        </span> <br/> 
      </td> 
    </tr> 
  </tbody> 
</table>

<table class="frontpage-paper-container"> 
  <tbody> 
    <tr> 
      <td> 
      </td> 
      <td>
        <span class="paper-title"> Revisiting Logistic-softmax Likelihood in Bayesian Meta-Learning for Few-Shot Classification
        </span><br/>
        <span class="author"> T. Ke*, H. Cao*, Z. Ling, F. Zhou
        </span> <br/> 
        <span class="venue"> Submitted, 2023.
        </span> <br/> 
      </td> 
    </tr> 
  </tbody> 
</table>

<style>
    table { 
  }
  
  .titletext {
      color:#111;
      font-size:30pt;
      font-weight:normal;
      text-align : left;
      padding: 30px 0px;
      font-family: 'MyriadProSemibold';
  }
  
  .container700 {
      width: 1040px;
      filter: alpha(opacity=0.97);
      opacity: 0.98; 
      margin: 20px auto;	
      text-align: left;
      border-style: solid;
      border-width: 2px;
      border-color: #333;
      background: #FFF;
      padding: 0px 30px 30px 30px;
      border-radius: 7px;
      -webkit-border-radius: 7px;
      -moz-border-radius: 7px;
      box-shadow: 10px 10px 30px #333333;
      -webkit-box-shadow: 10px 10px 30px #333333;
      -moz-box-shadow: 10px 10px 30px #333333;
  }
  
  .pubs {
      font-size:8pt;
  }
  
  .content {
      margin-left:150px;
      padding: 0px 0px 0px 30px;
  }
  
  .picture-border {
      border: 0px solid #ccc;
      margin-left: 15px;
      float: right;
  }
  
  #section-links {
      float: left; 
      width: 150px;
      background:#FFF;
      text-align:     left;
  }
  
  .bottom {
      clear:	left;
  }
  
  .photo {
      margin-left: 10px;    
      margin-right: 20px;
      margin-bottom: 15px;
      border:0;
      border-radius: 15px;
  }
  
  .frontpage-paper-container
  {
      margin-top: 1.0em;
      border-collapse: collapse;
      padding: 0px;
      margin-left: 0px;
  }
  
  .frontpage-paper-container td {
      vertical-align: top;
      padding: 0;
  }
  
  .frontpage-paper-container .author {
      color: #525252;
  }
  
  .frontpage-paper-container .venue {
      color: #525252;
      font-style: italic;
  }
  
  .frontpage-paper-container .paper-image {
      width: 100px;
      height: 70px;
      border: 2px solid rgb(51, 51, 51);
      margin-right: 5px;
  }
  
  .talk {
      font-size: 80%;
      line-height: 100%;
  }
  
  .talk .date {
      font-size: 90%;
  }
  
  .talk tr {
      vertical-align: top;
  }
  
  .old {
      color: #aaaaaa;
  }
  
  .paper-title {
      font-family: 'MyriadProSemibold';
  }
  
  .paper-image {
      float: none;
      margin: 0px 15px 0px 0px;
  }
  
  h2 {
      font-family: 'MyriadProSemibold';
  }
  
  .section-title {
      margin-top: 35px;    
      margin-bottom: 0px;    
  }
  
  .publication-lens .publication-title {
      font-family: 'MyriadProSemibold';
  }
  
  div.section {
      margin: 8px 0px;
  }
  
  div.abstractSection {
      margin: 8px 0px;
      width: 90%;    
  }
  
  .sectionContainer ul {
      margin: 5px 0px;
      padding-left: 12px;
  }
  
  .lowercase-number {
      font-size: 80%;
  }
  
  .award {
      font-variant: small-caps;
      color: #CC0829;
  }
  
  .authors {
      font-size: 80%;
  }
  
  .paper-img {
      border-radius: 15px;
      border: 0.5px solid #ddd;    
      width: 100px;
      height: 85px;    
  }
  
  .img-holder {
      width: 100px;
      height: 85px;    
      padding-right: 15px;
      padding-top: 3px;
      vertical-align: middle;
      display: table-cell;
  }
  
  .research-title {
      margin-top: 40px; 
      margin-bottom: 10px; 
      margin-left: -20px;
      font-size: 150%;
      font-family: 'MyriadProRegular';
      color: #385792;  
  }
  
  .research-title-sep {
      margin-top: 0px; 
      margin-bottom: 0px;     
      margin-left: -20px;
      border: none;
      height: 3px;
      /* Set the hr color */
      color: #333; /* old IE */
      background-color: #333; /* Modern Browsers */
  }
  
  .research-subtitle {
      margin-top: 25px; 
      margin-bottom: 5px; 
      margin-left: 0px;
      font-size: 120%;
      font-family: 'MyriadProRegular';        
      color: #385792;    
  }
  
  .research-subtitle-sep {
      margin-top: 0px; 
      margin-bottom: -2.5px;         
      margin-left: 0px;
  }
  
  .research-intro {
      margin-top: 0px; 
      margin-left: -20px;
  }
  
  .research-subintro {
      margin-top: 5px; 
      margin-bottom: 5px; 
      margin-left: 0px;
  }
  
  .fixed-footer {
      position: fixed;
      left: 50%;
      transform: translateX(-50%);
      bottom: 0;
      height: 43px;
      line-height: 43px;    
      background-color: #767676;
      opacity: 0.9;
      filter: alpha(opacity=85); /* For IE8 and earlier */    
      border: 0px;
      border-style: solid;
      border-color: #333;
      border-radius: 0px;
      width: 1100px;    
      font-size: 120%;
      text-color: #FFFFFF;
      box-shadow: 10px 10px 30px #333333;
  }
  
  .fixed-footer a {
      text-decoration: none;
      color: #ECEBE4;
      font-family: 'MyriadProRegular';
  }
  .fixed-footer a:hover {
      color:			#CAD2C5;
  }
</style>
