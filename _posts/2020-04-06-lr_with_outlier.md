---
title: "OR-3: Linear Regression with Outlier Selection"
subtitle: "A QCP formulation"
tags: [optimization, regression]
category: OR
date: 2020-04-06 10:15 #'%Y-%m-%d %H:%M:%S'
# 
layout: post
---

<html xmlns="http://www.w3.org/1999/xhtml" lang="" xml:lang="">
<head>
  <meta charset="utf-8" />
  <meta name="generator" content="pandoc" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes" />
  <meta name="author" content="Chuwen Zhang" />
  
  <style>
    code{white-space: pre-wrap;}
    span.smallcaps{font-variant: small-caps;}
    span.underline{text-decoration: underline;}
    div.column{display: inline-block; vertical-align: top; width: 50%;}
    div.hanging-indent{margin-left: 1.5em; text-indent: -1.5em;}
    ul.task-list{list-style: none;}
  </style>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.11.1/katex.min.js"></script>
  <script>document.addEventListener("DOMContentLoaded", function () {
   var mathElements = document.getElementsByClassName("math");
   for (var i = 0; i < mathElements.length; i++) {
    var texText = mathElements[i].firstChild;
    if (mathElements[i].tagName == "SPAN") {
     katex.render(texText.data, mathElements[i], {
      displayMode: mathElements[i].classList.contains('display'),
      throwOnError: false,
      fleqn: false
     });
  }}});
  </script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.11.1/katex.min.css" />
  <!--[if lt IE 9]>
    <script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.3/html5shiv-printshiv.min.js"></script>
  <![endif]-->
  <script src="https://cdn.jsdelivr.net/npm/mermaid@8.4.0/dist/mermaid.min.js"></script>
  <script>mermaid.initialize({ startOnLoad: true });</script>
</head>
<body>
<nav id="TOC" role="doc-toc">
<ul>
<li><a href="#linear-regression-with-outlier-selection">Linear Regression with Outlier Selection</a>
<ul>
<li><a href="#the-model">The model</a>
<ul>
<li><a href="#notation">Notation</a></li>
<li><a href="#decision">Decision</a></li>
</ul></li>
<li><a href="#quick-results">Quick results</a>
<ul>
<li><a href="#remark">Remark</a></li>
</ul></li>
</ul></li>
</ul>
</nav>
<h1 id="linear-regression-with-outlier-selection">Linear Regression with Outlier Selection</h1>
<h2 id="the-model">The model</h2>
<p>We use the usual notations in a linear regression problem:</p>
<h3 id="notation">Notation</h3>
<ul>
<li><span class="math inline">X</span> - data matrix of <span class="math inline">n</span> samples, where <span class="math inline">X = \begin{bmatrix}x_1^T\\...\\x_n^T\end{bmatrix}, x_i \in \mathbb{R}^m, i=1,...,N</span></li>
<li><span class="math inline">y</span> - response</li>
</ul>
<h3 id="decision">Decision</h3>
<p>Now we have the regression model: <span class="math display">\hat{y} = \beta^Tx</span></p>
<ul>
<li><span class="math inline">\beta</span> - model coefficients, <span class="math inline">\beta \in \mathbb{R}^m</span></li>
<li><span class="math inline">q</span> - <span class="math inline">0-1</span> decision on whether to keep the sample (else classified as one of the <strong>outliers</strong>)</li>
<li><span class="math inline">r</span> - residue of the estimates: <span class="math inline">r_i = |y_i - \hat{y_i}|</span>, or <span class="math inline">r \ge \|y-\hat{y}\|</span>, where <span class="math inline">\|.\|</span> is the <span class="math inline">\mathcal{L}_1</span> norm</li>
</ul>
<p>The model minimizes sum of “absolute selected loss”:</p>
<p><span class="math display">\begin{aligned}\min_{q,r,\beta} L_{\bf abs} &amp;= \sum_i r_i\cdot q_i \\
 &amp;=\begin{bmatrix} r\\q \end{bmatrix}^T\begin{bmatrix} 0 &amp; \frac{1}{2}\mathbf{I}_n\\  \frac{1}{2}\mathbf{I}_n &amp;0 \end{bmatrix}\begin{bmatrix} r\\q \end{bmatrix} &amp; \end{aligned}</span></p>
<p>To make the loss function semi-definite, let:</p>
<p><span class="math display">\mathbf{Q} = \lambda\cdot \mathbf{I}_n + \begin{bmatrix} 0 &amp; \frac{1}{2}\mathbf{I}_n\\  \frac{1}{2}\mathbf{I}_n &amp;0 \end{bmatrix} \in \mathcal{S}_{++}</span> whiling setting scaling parameter <span class="math inline">\lambda</span> properly (say <span class="math inline">1</span>). We have a binary quadratic programming model</p>
<p><span class="math display">\begin{aligned}\min_{q,r,\beta} L &amp;=\begin{bmatrix} r\\q \end{bmatrix}^T\mathbf{Q}\begin{bmatrix} r\\q \end{bmatrix} \\ s.t. &amp;\\ &amp;r \ge \|y-X\beta\|_1\\ &amp; \sum_{i=1}^n q_i \ge n_0 &amp;q \in \mathcal{B}^n, n_0 \le n \\\end{aligned}</span></p>
<p>The second set of constraints is the regularization on number of selections, since it’s monotonically better to <strong>unselect</strong> a sample, a lower bound <span class="math inline">n_0</span> is needed.</p>
<h2 id="quick-results">Quick results</h2>
<p>We now choose <span class="math inline">n_0</span> experimentally. The computations are done in the following environment.</p>
<pre class="env"><code>- julia 1.4
- gurobi 9.0</code></pre>
<h3 id="remark">Remark</h3>
<ul>
<li>Gurobi can do the objective directly as above.</li>
<li>If you are using Mosek, pls. translate into a conic formulation.</li>
</ul>
<p>The chart below is the computational results on minimum number of selections vesus <span class="math inline">R^2</span></p>
<p><img src="/assets/img/lr_results.png" /></p>
</body>
</html>