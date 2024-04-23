---
layout: post
title: Instalando o rbenv no openSUSE Leap
subtitle: Rápido prático e sem dor!
gh-repo: robsonafsilva/notepad
gh-badge: [star, fork, follow]
tags: [ruby, openSUSE, linux]
comments: true
mathjax: true
author: Robson
---

Começamos com as dependências necessárias.

```sudo zypper install git gcc automake gdbm-devel libyaml-devel ncurses-devel readline-devel zlib-devel libopenssl-devel readline-devel```

Instalação do Rbenv: clonando o repositório.

```git clone https://github.com/rbenv/rbenv.git ~/.rbenv```

Adicionar o rebenv ao .bashrc

```echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc echo 'eval "$(rbenv init -)"' >> ~/.bashrc```

Instalar o plugin do ruby-build

```git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build```

Recarregar o Shell

```exec $SHELL -l```

Agora é só utilizar

**Listar as versões estáveis:**

`rbenv install -l`

**Listar todas as versões:**

`rbenv install -L`

**Instalar uma versão do Ruby:**

`rbenv install 3.1.2`

**Definir a instalação como local**

```rbenv local 3.1.2```

**Para definir como global**

```rbenv global 3.1.2```

Lembrando que no openSUSE não é indicado usar como global, devido a utlização do ruby nos componentes do sistema.
Melhor opção é gerenciar o ruby de forma local no diretório dos seus projetos.


[Documentação Oficial](https://github.com/rbenv/rbenv)

