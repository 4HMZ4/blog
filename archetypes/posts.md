---
title: {{ replace .TranslationBaseName "-" " " | title }}
date: {{ .Date }}
author: "ahmza"
draft: true
weight: 

tags: [""]
description: ""
canonicalURL: "{{ .Page | absURL }}"

cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    searchHidden: false
showtoc: true
searchHidden: true
---
