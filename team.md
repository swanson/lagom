---
layout: default
title: Team
nav: main
permalink: /team/
---
<div class="sidebar-module">
	<h4><a href="/team">Team</a></h4>
  <ul>
	  <!-- for loop goes in filename order: order people using date in filename -->
  {% for person in site.categories.team %}
  <li style="list-style-type:none">
    {{person.position}}:
    <!--Used to prevent email spam-->
    <script type="text/javascript">
      document.write('<a href="mailto:' + '{{person.email-user}}' + '@' + '{{person.email-host}}' + '">')
    </script>
	{{person.firstname}} {{person.lastname}}</a>
  {% endfor %}
  </ul>
</div>