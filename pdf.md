---
layout: default
---

<div class="row">
  <div class="col-md-6">
    <h3>内链</h3>
    <div class="posts">
      {% for pdf in site.static_files %}
        {% if pdf.path contains 'pdfs' %}
        <div class="entry">
          <a href="{{site.baseurl}}{{pdf.path}}">{{pdf.basename}}</a>
        </div>
        {% endif %}
      {% endfor %}
    </div>
  </div>
  <div class="col-md-6">
    <h3>外链</h3>
    <div class="entry">
      <a href="https://pan.baidu.com/s/1WH2omt6yGlOUXk9lQmTOUw" target="_blank">机器学习_周志华.pdf</a>
    </div>
    <div class="entry">
      <a href="https://pan.baidu.com/s/1VhnmxBfXMbCiIm4Dkm57qg" target="_blank">Reinforcement Learning: An Introduction</a>
    </div>
  </div>
</div>
