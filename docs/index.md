---
layout: page
title: IBPSA Project 1
permalink: index.html
---

<div class="starter-template">
  <h1>Modelica Challenge Problems</h1>
  <p class="lead">
    Collection of challenge problems for Modelica tool developers
  </p>
</div>

This repository collects challenge problems in the area of buildings and
district energy system simulation.
The collection is meant to assist tool developers in improving Modelica
simulation backends, their solvers and diagnostic messages.

<table class="table_with_header">

<thead valign="bottom">
<tr>
<th>Model</th>
<th>Tool</th>
<th>Summary</th>
<th>Status</th>
</tr>
</thead>
<tbody valign="top">
{% for case in site.cases %}
<tr>
  <td>
  {{ case.model }}
  </td>
  <td>
  {{ case.tool }}
  </td>
  <td>
  {{ case.excerpt }}
  <a href="{{ site.baseurl }}{{ case.url }}">Details.</a>
  </td>
  <td>
  {{ case.status }}
  </td>

</tr>
{% endfor %}
</tbody>
</table>
