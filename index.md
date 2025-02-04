---
title: Home
layout: home
nav_order: 000
---

# CESSDA Technical Guidelines

These guidelines define how CESSDA products are [developed]({% link software/index.md %}),
by following CESSDA's implementation of the
[EURISE Network Technical Reference]({{ site.technical-reference.url }}).

The guidelines also define how CESSDA [operates services]({% link services/index.md %}) powered by these products.

A description of the [Technical Infrastructure]({% link technical-infrastructure/index.md %})
that CESSDA provides for the products to run on is also included.
This includes information about the underlying architectural principles.

The [Software Maturity Levels]({% link sml/index.md %})
that form the basis for CESSDA Quality Assurance are also included.

Changes since previous version:

* Added [Metadata Handling in CESSDA]({% link metadata/index.md %}) section
* Added [CESSDA Content Management]({% link content-management/index.md %}) section which contains details
  on how to apply for a Vocabulary Service Agency
* Added a [Vocabulary Service Agency application form]({% link forms/cvs-agency.md %})
* Added [Availability and Status page]({% link technical-infrastructure/observability/availability.md %})
* Restructured contents to improve findability.

{% if site.development_status %}
  {% if site.jenkins_job %}
  Built by Jenkins: [{{site.jenkins_job}}]({{site.jenkins_job}})
  {% else %}
  **This is a development build!**
  {% endif %}
{% else %}
This is version {{ site.version }}, released on {{ site.time | date: "%d %B %Y" }}.
{% endif %}
