---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: false
type: "projects"
tags: ["project"]
categories: ["Projects"]
description: "Project: {{ replace .Name "-" " " | title }}"
repo: ""   # GitHub link if available
demo: ""   # Live demo link if available
---
