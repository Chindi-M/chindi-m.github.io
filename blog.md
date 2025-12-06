---
layout: page
title: Blog
---

# 📝 Blog

I write about **cloud architecture, DevSecOps, automation, and platform engineering**—with a focus on practical, actionable content that you can apply to your own projects.

---

## 📚 All Posts

{% for post in site.posts %}
### [{{ post.title }}]({{ post.url }})
<small class="text-muted">{{ post.date | date: "%B %d, %Y" }} • {% for tag in post.tags %}#{{ tag }} {% endfor %}</small>

{{ post.excerpt | strip_html | truncatewords: 30 }}

[Read more →]({{ post.url }})

---
{% endfor %}

---

## 🏷️ Topics I Write About

**🏗️ Infrastructure as Code**  
Terraform modules, best practices, multi-cloud strategies, and lessons from production deployments.

**☸️ Kubernetes & Containers**  
Cluster management, GitOps workflows, debugging production incidents, and container optimization.

**🔒 DevSecOps**  
Security scanning in CI/CD, policy-as-code, secrets management, and building secure pipelines.

**🤖 Automation**  
CI/CD pipelines, infrastructure automation, incident response, and eliminating toil.

**📊 Observability**  
Monitoring, logging, tracing, and building dashboards that actually help during incidents.

**💼 Career & Learning**  
Breaking into DevOps, building portfolios, interview preparation, and continuous learning strategies.

---

## 🎯 What Makes This Blog Different

**Public Projects, Not Tutorials**  
I write about public code I've built, not random tutorials.

**Honest About Failures**  
I share what went wrong, not just what worked. Failures teach more than successes.

**Actionable Code Examples**  
Every technical post includes working code you can use and adapt for your own projects.

**Beginner-Friendly Explanations**  
I explain concepts clearly without assuming you already know everything.

**Production-Focused**  
I write about patterns that work at scale, not just in demos.

---

## 💡 Blog Philosophy

**Quality over quantity** : I'd rather publish one great post per week than daily mediocre content.

**Learning in public** : This blog documents my public learning journey, mistakes and all.

**Community-driven** : Topics often come from questions people ask or problems I've seen in forums.

**Open to feedback** : Found an error? Have a suggestion? [Let me know](/contact).

---

## 🔔 Stay Updated

### RSS Feed
Subscribe via RSS to get new posts in your feed reader.  
🔗 [RSS Feed](/feed.xml)

### Social Media
Follow me for updates, shorter tips, and discussions:
- **GitHub**: [github.com/chind-m](https://github.com/chindi-m)

---

## 📬 Request a Topic

Have a question or topic you'd like me to cover? I'm always looking for ideas.
[Send me a message](/contact) with your suggestion, and I'll consider it for a future post.

---

## 📊 Blog Stats

**Total Posts:** {{ site.posts | size }}  
**Most Recent:** {{ site.posts.first.date | date: "%B %Y" }}  
**Topics Covered:** DevOps, Kubernetes, Terraform, Security, Cloud Architecture, Career Advice

---

## 🤝 Guest Posts & Collaborations

Interested in collaborating on content or writing a guest post? I'm open to:
- Technical deep-dives on interesting problems
- Career stories from other DevOps practitioners
- Tool comparisons and evaluations

[Reach out](/contact) if you have an idea!

---

*Happy reading! If you find something useful, consider sharing it with your network.*