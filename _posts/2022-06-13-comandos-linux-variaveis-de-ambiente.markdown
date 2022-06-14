---
title: "Comandos Linux: Variáveis de Ambiente"
layout: post
date: 2022-06-13 21:00
tag:
  - linux
  - comandos
star: false
category: blog
author: keven
description: ""
hidden: false
---

_Assim como existem alguns comandos que podem ser usados no Shell, também existem variáveis que podem ser definidas pelo sistema, ou pelo usuário. Essas variáveis, podem ser utilizadas dentro de programas, pelo usuário ou pelo sistema. Elas são mais conhecidas como variáveis de ambiente. Neste artigo, o leitor será introduzido desde a criação de uma variável de ambiente, como a utilização da mesma. Também serão mostradas algumas variáveis de ambiente embutidos no **Bash** (o shell usado como base), o que é uma subshell, além de comandos embutidos._

## >_ O que é uma Variável e uma Constante?
No **Bash**, assim como nas **linguagens de programação**, existem variáveis. Variáveis nada mais são do que espaços na memória de um determinado computador destinados a armazenar dados, que podem ser alterados durante a execução do programa. O que as difere de constantes, é que as variáveis podem ser alteradas. Já as contantes, não podem ter seu valor modificado após a sua declaração.

Caso esse termo pareça novo para o leitor, uma analogia com um produto qualquer no mundo em que vive, pode auxiliar no entendimento. Imagine a seguinte situação: Você vai até um supermercado em uma determinada data, vê o preço de um suco de 2L de laranja por R$ 10,00. No ano seguinte, você vai no mesmo supermercado, e vê o mesmo suco, da mesma marca, por R$ 12,00. Pode-se extrair dois valores para a analogia: O suco de laranja, e o preço dele.

O suco, pode ser comparado com uma constante. Ele não mudou, seus ingredientes não são diferentes, e ainda continua tendo mesma embalagem e marca. Já o preço, é algo que pode ser mudado, tanto que o suco de R$ 10,00, virou R$ 12,00. Sendo assim, o preço deste suco, pode ser comparado com uma variável. Se mudar uma contante, assim como mudar os ingredientes de um suco de laranja, estará transformando aquele endereço na memória, em outro. No caso do suco, estaria transformando ele em outro produto!

O mesmo não acontece quando o preço daquele produto se modifica. O endereço (o suco de laranja), continua sendo o mesmo. Só mudou o seu valor(preço).

## >_ Declarando uma variável no Bash
Para realizar a declaração de uma variável no **Bash**, use a seguinte sintaxe:
```bash
nome_da_variavel=valor_da_varivavel
```
Os nomes das variáveis são limitados a caracteres alfanuméricos. Como visto acima, não pode existir espaço entre o *nome da variável* e o *valor* dela. O uso de espaço só é permitido no valor da variável quando utiliza-se algo chamado *quoting*. Isso será melhor explicado em outros artigos, mas em resumo, é permitido usar espaço quando o texto é envolto por aspas duplas ou simples.

Para ler o conteúdo armazenado na variável, use o comando embutido do **bash**, **echo**. Usando o comando, em conjunto com a variável, pode ser visualizado o seu valor. Por exemplo: 
```bash
echo $nome_da_variavel
```

Entretanto, essa variável está limitada para a *sessão* em que está no **Bash**. Caso abra uma nova *sessão*, e tente executar o mesmo comando, o valor declarado na variável anteriormente não vai ser mostrado na tela. Veja também que para usar uma variável, deve ser colocado um **$** na frente do nome dela.

## >_ O que são sessões no Bash?
O **Bash**, é composto por *sessões*. Cada *sessão* possuí suas variáveis de ambiente. Existem *sessões* que estão dentro de *sessões*, elas são chamadas de *subsessões* ou subshell. Elas podem ser usadas para executar pequenos comandos com algum retorno, por exemplo, e serem concatenados em um outro comando.

Toda vez que um script shell é executado, ele cria um subshell, um shell filho deste shell.

Para criar, você pode escrever algum comando entre parenteses **()**, ou entre crase. Em alguns casos, será necessário a inclusão de um **$** antes dos parenteses. Um exemplo:

```bash
# Executando com $()
VAR=$(echo valor)
```

```bash
# Executando com crase ``
VAR=`echo valor`
```

Em palavras mais simples, um subshell, é um processo filho deste shell principal. Caso abra uma nova *sessão* do **bash**, dentro dessa em que está, e não exporte as variáveis de ambiente, você não irá conseguir ler o valor desta variável:

```bash
VAR=1
bash
echo $VAR
```

Para que consiga ler o valor dessa variável dentro destes shells, é necessário incluir o comando embutido **export** antes da variável. O seguinte comando irá funcionar:

```bash
export VAR=1
bash
echo $VAR
```

## >_ Variáveis pré-definidas e dinâmicas no Bash
O Bash possuí algumas variáveis pré-definidas, que podem ser de grande auxilio para o usuário. As principais seriam:
- **DISPLAY** = Ele define qual o display deve exibir suas janelas
- **HISTFILE** = Caminho para o histórico de comandos do usuário
- **HISTFILESIZE** = Quantidade de linhas/comandos que podem ser armazenados no arquivo de histórico
- **HOME** = Caminho para o diretório *home* do usuário
- **PATH** = Diretório em que o Linux irá procurar por arquivos executáveis
- **PS1** = Aparência do seu Terminal (Prompt de Comando)
- **PWD** = Diretório atual
- **OLDPWD**= Diretório que estava anteriormente.
- **TERM** = Terminal que está aberta a *sessão*
- **LOGNAME** = Nome do usuário atual

Além dessas variáveis, existe também algumas variáveis que são definidas dinamicamente no shell. As principais seriam:
- **$$** = Mostra o PID do Shell Atual (PID seria um número que identifica o processo)
- **$!** = Mostra o PID do último processo executado
- **$?** = Mostra o código de retorno do último processo/comando executado, sendo o codigo **0** como sucesso.
- **~** = Corresponde ao diretório *home* do usuário.
- **~root** = Corresponde ao diretório *home* do usuário **root**.

Essas variáveis embutidas atuam quase como comandos, já que devolvem valores que podem ser especialmente úteis até mesmo em scripts. 

## >_ Comandos Embutidos no Bash

Além de variáveis, existem alguns comandos embutidos no **Bash**. Alguns deles, inclusive, já foram mostrados. Os principais comandos embutidos no bash seriam:
- **alias** = Cria um alias (nome alternativo) para um comandos
- **exec** = Executa um comando em uma nova *sessão* do shell
- **echo** = Joga para a saída, um texto replicado, ou uma variável
- **env** = Sem argumentos, exibe as variáveis de ambientes, e os seus valores 
- **export** = Define uma variável de ambiente para a *sessão*, e para todas as *sessões* filhas dessa
- **pwd** = Mostra o diretório atual
- **set** = Define o valor de uma variável. Quando não usado com argumentos, exibe as variáveis já definidas
- **unset** = Remove uma variável da *sessão* do **bash**

## >_ Conclusão
Neste artigo, o leitor teve um resumo sobre variáveis de ambiente, bem como, sobre alguns comandos embutidos no shell. Nos próximos artigos, será mostrado um pouco mais sobre esses comandos, mostrando como podem ser usados na prática.

<div class="breaker"></div>
