---
title: "Comandos Linux: Alias"
layout: post
date: 2022-06-28 09:00
tag:
  - linux
  - comandos
star: false
category: blog
author: keven
description: "Neste artigo, falaremos um pouco mais sobre um dos comandos internos do linux: O Alias"
hidden: false
---

_No artigo anterior dessa série de artigos, o leitor foi introduzido um pouco sobre Variáveis de Ambiente, e Comandos Embutidos no Bash. No linux, existem alguns comando que são próprios do terminal. Esses comandos permitem realizar tarefas administrativas, ou então, tarefas que possibilitam manejar a forma como pode ser trabalhado com o bash. Neste artigo, o leitor será introduzido a conhecer um dos comandos built-in do linux: o alias._

## >_ O Comando Alias
O comando **alias**, é um comando embutido do *bash* que permite escrever atalhos de comandos, facilitando assim, o uso de comandos grandes/complexos no dia-a-dia. Algumas distribuições **linux** já possuem alguns **alias** configurados. Um deles, que pode ser citado é o **ll**:

```bash
# caso o alias não tenha sido definido pela distribuição que está usando, copie o comando, e cole no terminal
alias ll='ls -l' 
```

Quando um **alias** é definido, ele permite com que usando o atalho, neste caso o **ll**, seja executado na verdade o comando **ls -l**.

Para visualizar todos os **alias** presentes na sessão atual do *bash*, execute o comando **alias** sem nenhum argumento.

```bash
alias
```

## >_ O que vem primeiro: o comando ou o atalho?
Toda vez que um comando é digitado no *bash*, é feito primeiro a checagem se o comando possuí um atalho. Se ele possuir, então ele é executado. Caso ele não possua, é feita a verificação se aquele comando existe.

Para fazer o teste, o leitor pode definir um **alias** para um comando com o nome **cd**:
```bash
alias cd="echo acho que não mudou de diretorio :\("
```
Ao digitar o comando **cd**, o texto do comando **echo** será escrito ao invés de navegar entre diretórios, como deveria fazer o comando **cd**.

Isso acontece porque o *bash*, antes de executar um comando, faz a leitura do texto enviado pelo terminal, para então, posteriormente, executá-lo. Para melhor entendimento, o fluxo abaixo mostra a ordem de execução de um comando no *bash*:
- Bash faz a leitura do comando digitado
- Bash verifica se existe algum alias em tempo de leitura do comando
- Bash passa para a fase de execução
- Bash executa o comando, se ele estiver presente no $PATH

Quando ele encontra algum atalho de comando na fase de leitura do comando digitado, ele faz a substituição deste atalho na fase de execução. Caso o **alias** seja um atalho de um atalho, ele executa a conversão até chegar a um comando. Se o fim desse **alias** não for um comando, ou for um comando que não está presente no **PATH**, ele dará erro em tempo de execução, informando que o comando não existe.

## >_ Criando um Alias
A sintaxe de uso do comando **alias**, não é complicado, mas simples. É possível criar um **alias** seguindo o molde abaixo:
```bash
alias COMANDO_ATALHO="COMANDO_ORIGINAL"
```
O *COMANDO_ORIGINAL* pode ser colocado entre _aspas duplas_, _simples_, e _crase_, permitindo definir *flags*, e argumentos. O _COMANDO_ATALHO_ não pode conter **/**, **$**, **`**, e **=**. Além disso, todo o argumento a mais passado para o comando de atalho, é redirecionado como um argumento do comando original. Em casos que isso não é algo desejado, adicione **;** no final do **alias**, para que novos argumentos sejam entendidos como comandos, e não como um argumento do comando executado pelo **alias**.
```bash
alias la="ls -la;"
```
Se o **alias** acima for executado passando algum argumento a ele, o argumento será executado como comando.
```bash
la -R # O alias *la* será interpretado, mas o -R será tratado como se fosse um comando, então, irá falhar.
```

Também é importante definir um valor ao **alias**. Se nenhum valor for definido para o comando de atalho, ele não será criado.
```bash
alias lf # Se o usuário digitar **lf** no terminal, será informado que o comando não existe.
```


Algo que é incomum, mas possível, é a criação de um **alias** que tem um **alias** como seu comando. 
```bash
alias rmi="rm -i"
alias rrm="rmi -r"
```
Se executar o comando **rrm** ele irá interpretar o **alias** **rmi** passando o **-r** como argumento, que executará o comando **rm -i -r**.

## >_ Criando um Alias Global
Para realizar a criação de um **alias** que possa ser usado entre sessões, será necessário definir ele em um arquivo. O arquivo, no caso do *bash*, seria um dos arquivos descritos abaixo:
- ~/.bashrc 
- ~/.bash_profile
- ~/.profile
- ~/.bash_login
- /etc/profile
- /etc/profiles.d/\*
- /etc/bash.bashrc
- /etc/bashrc

Se deseja apenas que o **alias** esteja em uso em sessões do *shell* que sejam efetuados *login* via *console*, ou então via **SSH**, defina o **alias** em arquivos **profile**, já que eles são carregados em *shells de login* somente. 

Caso queira apenas  que o usuário atual tenha o **alias**, defina em um dos arquivos ocultos na **HOME** do usuário. Ou, se preferir tornar global, ao ponto de que todos os usuários tenham determinado **alias**, defina-o em algum dos arquivos que esteja no diretório **/etc**. e presentes na lista acima.

## >_ Removendo um Alias
Quando cria um **alias**, é possível que tenha o desejo depois de removê-lo. O comando **unalias** pode ser usado para realizar essa remoção:
```bash
unalias rm
```
Assim como o **alias** executado no terminal, ele é apenas definido na sessão atual. Se precisar remover um **alias** definido para todas as sessões, a melhor alternativa é descobrir onde ele está sendo definido, e então removê-lo do arquivo. Caso isso não seja possível, pode ser informado em um dos arquivos acima (dependendo de seu uso) o comando **unalias** e o nome de seu atalho.

## >_ Conclusão
Neste artigo, o leitor obteve informações de como visualizar, criar, gerenciar, e remover atalhos para comandos existentes no sistema. Esses atalhos são poderosos, podendo agilizar o dia-a-dia de quem trabalha com **linux**, a medida que necessita cada vez mais usar argumentos e _flags_ em determinados comandos. No próximo artigo, o leitor será introduzido a outro comando built-in poderoso: o comando exec.

<div class="breaker"></div>
