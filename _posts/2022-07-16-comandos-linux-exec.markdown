---
title: "Comandos Linux: Exec"
layout: post
date: 2022-07-17 22:00
tag:
  - linux
  - comandos
star: false
category: blog
author: keven
description: ""
hidden: false
---

_No artigo anterior desta série de artigos, o leitor obteve conhecimentos sobre o comando alias. Ele é um dos comandos embutidos no bash. Esses comandos embutidos, também conhecidos como built-in, apesar de parecerem simples, são poderosos, já que possibilitam uma comunicação mais rápida com recursos do sistema. Um desses comandos embutidos, permite com que o usuário utilize descritores de arquivos de uma forma mais prática. O comando exec, permite realizar essa manipulação, além de fazer a sua função principal: executar um novo programa no mesmo processo em que está, sem utilizar uma subshell. Neste artigo, o leitor será introduzido ao comando exec e aos conceitos de sistemas operacionais, exec e fork._

## >_ O Comando Exec
O **comando exec** permite executar um comando sem criar uma *subshell*. Com ele, é até mesmo possível redirecionar descritores padrões de arquivos. Ele permite executar comandos com qualquer quantidade de argumentos. Qualquer argumento passado para o **exec**, será tratado como argumento do comando que está sendo passado. É importante frisar também que, quando não usado com descritores, ele utiliza códigos de retorno para o *shell*. Isso faz com que, ao final da execução daquele programa, o processo seja encerrado.

Um exemplo de como funciona a sua interpretação, seria a seguinte:
```bash
exec {comando} {1...N argumentos do comando}
```

O **comando exec**, segue o mesmo conceito do **exec** presente em **sistemas operacionais**.

Em **sistemas operacionais**, um processo pode ser filho de outro, o que é chamado de **fork** (existem alguns detalhes que serão explicados mais adiante). Já o **exec**, permite a execução de um processo, sem a criação de um processo filho.

Se é a primeira vez que o leitor se depara com esses termos, não deve se preocupar. Ao decorrer do artigo, serão dadas mais explicações sobre os termos tratados acima.

## >_ Uma breve introdução ao Fork
Anteriormente, foi dito que um processo filho pode ser considerado um **fork**. Entretanto, um processo filho possuí além de **fork**, também a função **exec**, e no caso de *subshell*, também possuí outras funções. Lembrando que está sendo falado do **exec de sistemas operacionais**, e não do **comando exec** presente no *linux*.

O **fork** tem como finalidade bifurcar, ou seja, criar um processo, e executar em paralelo o restante do código que ainda não foi executado. Existem formas de fazer o processo pai esperar o processo filho terminar sua execução. Na *Linguagem de Programação C*, por exemplo, pode ser usado o *Wait System Call*. Por padrão, caso o processo pai termine, o processo filho ainda continua sua execução em *background*, se ainda não tenha sido finalizado.

Caso o leitor tenha o interesse de ver isso sendo executado, abaixo segue um código de exemplo de *fork* com a utilização de *Wait System Call*, para esperar a finalização do processo filho.
```c
#include <stdio.h>
#include <unistd.h>
#include <wait.h>

int main() {

  if (fork() == 0) {
    printf("Processo Filho: %d\n", getpid());
  } else {
    printf("Processo Pai: %d\n", getpid());
  }

  wait(NULL);
  return 0;
}
```

## >_ Uma breve introdução ao Exec
O **exec**, diferentemente do **fork**, não cria um processo. Ele é uma funcionalidade do sistema operacional que executa um arquivo que possa ser executado, substituindo o arquivo executável já existente naquele determinado processo. Como ele não faz a criação de um novo processo, mas utiliza o processo já em execução, não há uma mudança de **PID**. Entretanto, todo o contexto do processo se modifica, tais como o código e os dados, pelos do novo programa em execução

Entendendo isso, fica mais simples do leitor entender o que o **comando exec** no *linux* faz.

Abaixo, da mesma maneira que feito com o **fork**, um exemplo de código utilizando-se do **exec**:
```c
#include <stdio.h>
#include <unistd.h>

int main() {

  printf("Mensagem mostrada na tela. Logo abaixo, uma listagem de diretório: \n\n");
  char *args[] = {"/usr/bin/ls", "-l", NULL};
  execv(args[0], args);
  
  printf("Esse texto nunca será mostrado =(");
  return 0;

}

```

## >_ Voltando ao Comando Exec...
Com essa introdução à alguns conceitos de **sistemas operacionais**, fica mais simples explicar o que o **comando exec** faz. Na prática, ele faz o que foi descrito sobre o **exec de sistemas operacionais**.

Existem alguns argumentos nele que são úteis:
- **-c** = É usado para executar o comando sem definir as variáveis de ambiente definidas por padrão. É mais usado para caso queira isolar e tornar mais ágil a execução, já que não há o carregamento das variáveis de ambiente. 
- **-l** = É usado para executar o comando como um *shell de login*. Na prática, seria carregando todas as variáveis presentes nos arquivos de configuração e *profile*, assim como é feito em um *shell* convencional.
- **-a** = Altera o nome do comando ao qual você está executando com o **comando exec**. Ao digitar o comando **ps** ou **top**, verá que o processo do **exec** está com outro nome no comando executado. 

Apesar de existirem esses argumentos, eles serão usados apenas em algumas situações bem específicas. Uma das formas mais comuns do uso do **comando exec**, seria a utilização de *redirecionadores* e *descritores de arquivos*.

### Redirecionadores
Os redirecionadores de terminal, podem ser usados em conjunto com o **exec** para fazer com que os resultados de **STDERR** e **STDOUT** sejam ocultados. Isso permite com que execute uma série de comandos, sem obter nenhuma saída no terminal. Além disso, é possível redirecionar eles para um ou mais arquivos diferentes.

Existem dois símbolos principais para o uso de redirecionadores: o **>** e o **<**. Existem alguns outros como o **>\>**, **<\<\<**, e **<\<**, entretanto, para não ficar longo este artigo, eles não serão abordados.

Caso queira que todas as saídas do **STDOUT** executados pelo **exec**, sejam salvas em um arquivo, execute o seguinte comando:
```bash
exec >/tmp/exec.log
# ou exec 1>/tmp/exec.log
```

Se deseja redirecionar dois dos descritores de arquivos padrões, o **STDOUT** e **STDERR**, redirecione o **STDERR**, para o **STDOUT**:
```bash
exec 1>/tmp/exec_all.log 2>&1
```

Para voltar ao normal, use o comando abaixo, redirecionando a saída dos comandos para o terminal novamente:
```bash
exec 1>/dev/tty
#ou exec >/dev/tty
```

É possível também fazer o redirecionamento de todas as entradas, executando o comando abaixo:
```bash
exec cat </etc/passwd
```

### Descritores de Arquivos
No *linux*, existe algo que é chamado de descritores de arquivos, isso se dá ao fato de que tudo no *linux* são arquivos. Estes descritores seriam como atalhos, que permitem com que alguns recursos sejam manejados com facilidade. Com os descritores de arquivos, é possível trabalhar com a saída do terminal, entradas do teclado e abrir arquivos. Anteriormente, foi observado o seu uso nos redirecionadores. Abaixo, Será explicado um pouco como pode-se manipular esses descritores usando o **comando exec**.

Usando o *bash* como exemplo, ele normalmente trabalha com três descritores de arquivos sempre abertos:
- Um descritor de arquivo que serve de entrada padrão de dados
- Um descritor de arquivo que serve de saída padrão de dados
- Um descritor de arquivo onde as mensagens de erro são gravadas

Esses descritores são definidos em */usr/include/unistd.h* conforme a numeração abaixo:
- **0** = O Descritor de arquivo 0, simboliza a entrada de dados (normalmente é o teclado).
- **1** = O Descritor de arquivo 1, simboliza a saída de dados (normalmente, é o terminal).
- **2** = O Descritor de arquivo 2, simboliza a saída de erro (normalmente, é o terminal).
- **255** = O Descritor de arquivo 255, é um *backup* dos descritores padrões. Ele é usado apenas caso você os modifique.

Quando um processo é criado, ele herda as cópias desses descritores, que irão dizer para onde ele deve enviar, e receber, cada informação.

É possível redirecionar a entrada e saída (em simultâneo), criando um descritor e fazendo com que um arquivo seja criado, caso não exista:
```bash
exec 3<>arquivo_teste.txt
```

Dessa forma, é possível criar o **Descritor de Arquivo de número 3**, e usar ele tanto como saída para novas informações, como também como entrada das informações antigas. É importante entender que as informações inseridas após essa criação do descritor, não são lidas por ele, apenas as informações que já estavam armazenadas lá anteriormente.

Uma forma de fazer essa saída, seria utilizando o comando abaixo:
```bash
exec 1>&3
```

Como pode ser observado no comando acima, toda vez que deseja redirecionar a saída de um descritor para outro, é necessário colocar o **&** na frente da numeração do descritor. O código acima, redireciona o **descritor 1**, que no *bash* tem a sua função atrelada a saída padrão dos comandos, para o **descritor 3**. Com isso, a saída dos comandos serão direcionados para o **descritor 3**, cuja função é ler e escrever no *arquivo_teste.txt*.

É possível, em casos de redirecionadores de entrada, ler o que está armazenado nele:
```bash
read -u 3 line
echo $line
```

O comando **read **irá utilizar o argumento **-u**, cuja função é ler descritores de arquivos, permitindo com que ele leia uma linha deste descritor e armazene ela em uma variável chamada *line*. Essa é a forma mais comum de conseguir ler as entradas presentes nos descritores.

Outra boa prática, é sempre fechar os descritores que já foram utilizados. Assim como na programação de outras linguagens, não é bom abrir um arquivo e não fechar ele após usá-lo, também não é bom abrir um descritor de arquivo, e não fechá-lo depois. Para fechar um descritor de arquivos, utilize o seguinte comando:
```bash
exec 3>&-
```

Quando se trata de descritores numéricos, para fechar sempre será usada essa sintaxe para fechá-lo: **&-**.

Caso o descritor tenha outra numeração, utilize ela, ao invés do **número 3**. Por padrão, o limite máximo de descritores de arquivos que podem ser abertos, é de **1024** no *bash*. Caso queira saber o número exato em seu sistema, execute o comando:
```bash
ulimit -n
```

É possível também utilizar ao invés de números, algum nome de variável, como por exemplo *meudesc*. Dessa forma, é possível fazer a manipulação sem usar números diretamente. Para criar um descritor desta forma, utilize o seguinte comando:
```bash
exec {meudesc}>&1
```

O comando acima, fará com que todos os comandos redirecionados para o descritor **{meudesc}**, sejam enviados para a tela, devido ao redirecionamento do descritor criado para o **descritor 1**, que trata do **STDOUT**.

Caso execute o comando abaixo, ele será redirecionado para o **STDOUT**:
```bash
ls -l >&$meudesc
```

Sempre quando for usar este tipo de descritor, é necessário incluir o **&$** no nome dele.

Para fechar esse tipo de descritor, basta executar o comando abaixo:
```bash
exec {meudesc}>&-
```

### Outros usos comuns
Existem usos mais comuns do **comando exec** também, que não seriam a utilização de descritores de arquivos.

O uso mais comum, seria executar um comando e encerrar a sessão em que está, por exemplo:
```bash
exec echo valor
```

O comando **echo** será executado, e logo após encerrará a sessão do *bash* em que está. 

É possível também mudar de *shell*, sem criar uma *subshell*, naquela sessão atual. A vantagem disso, é não ter um processo pai responsável consumindo memória:
```bash
exec -l sh
```

O exemplo acima, substitui o *shell* atual pelo *sh*.

## >_ Conclusão
Neste artigo, foi mostrado ao leitor, o **comando exec**, e seus inúmeros usos. Ele é útil em manejar arquivos utilizando descritores, podendo ser usado para ler todas as linhas de um arquivo de uma forma mais eficiente do que utilizando comandos que fariam essa leitura, como o **comando cat**. Além disso, o leitor teve uma breve introdução de como funciona a criação de processos, e como alguns processos podem ser dependentes de outros.

Nem sempre um processo ser dependente de outro é uma má escolha. Cabe ao usuário decidir o que é o melhor para cada situação.

Gostou do artigo, tem alguma sugestão ou ficou com dúvidas? Deixe um comentário!

<div class="breaker"></div>
