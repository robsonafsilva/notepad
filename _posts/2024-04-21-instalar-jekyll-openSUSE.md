---
layout: post
title: Instalar o Jekyll no openSUSE Leap
subtitle: Rápido prático e sem dor!
thumbnail-img: /assets/img/jekyll-logo.webp
gh-repo: robsonafsilva/notepad
gh-badge: [star, fork, follow]
tags: [jekyll, openSUSE, linux]
comments: true
mathjax: true
author: Robson
---


As dependências necessárias.

```sudo zypper install -t pattern devel_ruby devel_C_C++ sudo zypper install ruby-devel```

Agora só instalar e usar:

Instalar o jekyll e o bundler.

```gem install jekyll bundler```

Cria um novo site:

```jekyll new nome-do-site```

Ir para dentro do diretório:

```cd nome-do-site```

Fazer o build do site e tornar ele disponível no servidor local.

```bundle exec jekyll serve```

Acessar em http://localhost:4000

[Documentação Oficial](https://jekyllrb.com/docs/)

