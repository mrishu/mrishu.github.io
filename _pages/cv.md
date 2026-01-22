---
layout: page
permalink: /cv/
title: cv
nav: true
nav_order: 5
# NOTE: Must replace dropbox share link's last part ?dl=0 with ?raw=1
cv_pdf: https://www.dropbox.com/scl/fi/b76w9amyubs6o22fxrisr/resume.pdf?rlkey=uskcwlw1tm8xc4gdfrvbtuzzo&raw=1
description:
---

<div style="
  text-align: right;
  margin-top: -3.0rem;
  margin-bottom: 0.8rem;
">
  <a href="{{ page.cv_pdf }}"
     target="_blank"
     title="Open CV as PDF"
     aria-label="Open CV as PDF"
     style="text-decoration: none;">
    <i class="fas fa-file-pdf" style="font-size: 2.0rem;"></i>
  </a>
</div>

<iframe
  src="{{ page.cv_pdf }}#toolbar=1&navpanes=0"
  style="
    width: 100%;
    height: 1200px;
    border: none;
  "
></iframe>
