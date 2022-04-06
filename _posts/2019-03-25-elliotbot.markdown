---
title: "ElliotBot"
layout: post
date: 2019-03-25 02:07
tag: 
  - bot
  - telegram
  - Guia Anônima
headerImage: true
projects: true
hidden: true # don't count this post in blog pagination
description: "Este é um projeto destinado para o gerenciamento de mensagens e uso administrativo do grupo no Telegram, Guia Anônima."
category: project
author: keven
externalLink: false
---

Este é um projeto destinado para o gerenciamento de mensagens e uso administrativo do grupo no Telegram, [Guia Anônima][link-telegram]

Para ter acesso ao repositório do projeto, acesse o link do [GitHub][link-github].

## Requisitos
Caso deseje utilizar este Telegram Bot, ou executá-lo, precisará dos seguintes pacotes, presentes no **requeriments.txt**:

* **certifi==2019.3.9**
* **chardet==3.0.4**
* **idna==2.8**
* **pyTelegramBotAPI==3.6.6**
* **python-dotenv==0.10.1**
* **requests==2.21.0**
* **six==1.12.0**
* **urllib3==1.24.1**

Para instalar todos, de uma única vez, utilize o comando abaixo:

{% highlight python %}
pip install pyTelegramBotAPI python-dotenv
{% endhighlight %}

Ou utilizando o arquivo **requeriments.txt**:

{% highlight python %}
pip install -r requeriments.txt
{% endhighlight %}

## Instalação
O processo de instalação é simples. Basta fazer um **git clone** do repositório.

{% highlight shell %}
git clone https://github.com/TheMrKeven/ElliotBot.git
{% endhighlight %}

Entretanto, mesmo após instalado, necessita de alguns ajustes.

## Configuração
O processo de configuração também é simples, mas exige certa atenção, para que tudo funcione corretamente. 
Uma configuração errada, acarretará em certos problemas na execução do código. É aconselhável a utilização de um ambiente virtual, entretanto, se deseja utilizá-lo sem um, 
tenha certeza de que o python utilizado, é **Python >= 3.6**. 

Para ver a versão do Python, utilize seguinte comando no terminal:
{% highlight shell %}
python --version
{% endhighlight %}

No arquivo **.env**, **settings.py** e **messages.py**, presentes 
no diretório **ElliotBot/src/config**, possuem algumas configurações a serem feitas.

* **messages.py**: Ficam localizadas as mensagens do bot. Caso deseje alterar 
alguma mensagem, pode ser feito neste arquivo.

* **.env**: Devem estar presentes nesse arquivo, o Token e o Link do Bot.
O link, é utilizado no Inline Keyboard, para direciona-lo ao chat Privado, ao apertar em **Regras**. 
O Token, deve ser obtido com o **BotFather**.

* **settings.py**: Todas as modificações, devem ser passadas a constantes deste arquivo,
por ele ser importado pelo **core.py**.

## Executando

Após ter configurado e instalado os requisitos, utilize o comando 
{% highlight shell %}
python bot.py
{% endhighlight %}

## Exemplos de Comandos

Aqui estarão listados alguns comandos já presentes no Bot, caso deseje adicionar um comando ao projeto, leia a seção [Como Contribuir][como-contribuir].

* **/start**: _Mostra as regras do grupo._ (__Privado somente__)
* **/rules**: _Mostra as regras do grupo. (__Privado, e no Grupo__) (__Grupo, Somente Admins__)_
* **/ban**: _Aplica banimento em membro do grupo (__Somente por Reply__) (__Somente Admins__)_
* **/canal**: _Mostra Link do Canal do Youtube __Guia Anônima___ (__Privado, e no Grupo__) (__Todos__)
* **/curso**: _Mostra Cursos do Instrutor @AdSEBR (__Privado, e no Grupo__) (__Todos__)_
* **/ctf**: _Mostra mensagem do CTF (__Privado, e no Grupo__) (__Todos__)_

<div class="breaker"></div>
[git-release]: https://img.shields.io/github/release/TheMrKeven/ElliotBot.svg
[license]: https://img.shields.io/github/license/TheMrKeven/ElliotBot.svg
[python-version]: https://img.shields.io/badge/python-3.6|3.7-blue.svg
[release-link]: https://github.com/TheMrKeven/ElliotBot/releases
[license-link]: https://github.com/TheMrKeven/ElliotBot/blob/master/LICENSE
[como-contribuir]: https://github.com/TheMrKeven/ElliotBot#como-contribuir
[link-telegram]:  https://guiaanonima.com
[link-github]: https://github.com/TheMrKeven/ElliotBot