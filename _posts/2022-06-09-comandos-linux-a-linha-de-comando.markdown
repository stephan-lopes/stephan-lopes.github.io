---
title: "Comandos Linux: A linha de Comando"
layout: post
date: 2022-06-09 20:00
tag:
  - linux
  - comandos
star: false
category: blog
author: keven
description: "O que é a linha de Comando Linux? Como podemos nos beneficiar dela?"
hidden: false
---

_No linux, temos um espaço, onde podemos conversar com recursos que são gerenciados pelo nosso sistema operacional, além de ter a possibilidade de usar ferramentas que podem nos auxiliar no dia-a-dia. Esse espaço se chama __Terminal__. Esse é o primeiro artigo de uma série de postagens abordando como funciona a linha de comando no linux, bem como mostrar os comandos essênciais que devem ser conhecidos pelo administrador desse sistema. Eles podem ser aproveitados por qualquer pessoa que deseja conhecer mais sobre linux_.

## >_ O que é um Sistema Operacional? 
Poderia começar de uma forma sem abordar isso, mas, é sempre bom contextualizar o que é um _Sistema Operacional_, afinal, o [Linux](https://www.linux.org/) é um deles. Existem hoje, diversos _Sistemas Operacionais_, tais como Unix, macOS, BSD, Minix, [Linux](https://www.linux.org), Solaris, e por ai vai. O que eles tem em comum? Todos eles possuem duas funções principais (e diria eu, essênciais), que seriam:

- __Fornecer a programadores, um conjunto de recursos mais simples de serem manipulados ao invés de recursos abstratos e confusos do hardware.__
- __Gerenciar os recursos do hardware__

Em outras palavras, sendo elas bem resumidas, um _sistema operacional_ é o responsável por fazer uma interface entre o usuário e o hardware do computador.

O [Linux](https://www.linux.org), não é um _Sistema Operacional_ completo, diferente de como a maioria das pessoas imagina. Ele é somente o **Kernel**. Ele faz as duas funções importantes, abordadas anteriormente. Porém, quem dá "vida" ao _Sistema Operacional_, são as distribuições, que vem com algumas interfaces de iteração com a máquina, sendo ela via texto ou gráfica, e também programas. Estes três pontos: **Kernel**, **Interface com Usuário**, e **Programas**, em conjunto, podem ser definidos como um _Sistema Operacional_.

## >_ Quer dizer que o Kernel é algo inútil?
Claro que não! Sem ele, não existe esse gerenciamento e nem mesmo a abstração entre o usuário e seu hardware. Ele é algo que não interagimos diretamente, mas que existe no núcleo de seu _sistema operacional_. Sem ele, as distribuições linux não existiriam.

Quando trabalhamos em uma linha de comando no [linux](https://www.linux.org), também conhecido como **SHELL**, estamos utilizando um programa, que pode manipular recursos do sistema, mas que ainda sim, não está no mesmo nível que o **Kernel**.

## >_ O Shell
O **Shell**, nada mais é do que uma interface, um interpretador de comandos. Nele, podemos executar scripts, e utilizar uma série de comandos para manusear o sistema, seja modificando algum componente importante, ou apenas utilizando ele como "ponte" para abrir um editor de texto.

Abordaremos o **Shell** conhecido como **BASH**(*Bourne-Again SHell*). Existem outros milhares de **Shell**, como o **ZSH**, **SH**, **CSH**, **KSH**, etc. Cada um tem sua particularidade, como por exemplo o **CSH** que permite o uso da *linguagem C*, de forma iterativa, e de **Shell Script**. Caso queira saber qual **Shell** está utilizando, execute o comando:

{%highlight bash%}
echo $SHELL 
{%endhighlight%}

Esse comando irá informar qual **shell** você está usando. O **Shell** em uso, é armazenado na variável de ambiente __$SHELL__.

## >_ Comando? O que é isso?
Um comando é algo que você executa dentro do terminal, que retorna algum resultado para você, mostrando, ou não, na tela, o seu resultado. Existem três tipos de comandos: 

- __Comando Interno do Shell(built-in)__
- __Binários localizados no PATH__
- __Scripts(sequência lógica de instruções interpretadas pelo Shell)__

Podemos definir que um programa também seja um comando. Não vemos ele sendo executado quando abrimos via interface gráfica, mas ele é utilizado toda a vez que abrimos um programa, como por exemplo, um navegador.

## >_ Conclusão
Neste artigo, foi abordado um pouco sobre o que é um _sistema operacional_, para contextualizar a utilidade do uso de linhas de comando no [linux](https://www.linux.org). Também, houve uma introdução sobre o que é um **Shell**, e o que é um comando. Nos próximos artigos, iremos falar um pouco mais sobre eles, e introduzir o leitor às variáveis de ambiente.

<div class="breaker"></div>

