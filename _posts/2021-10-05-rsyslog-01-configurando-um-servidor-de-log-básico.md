---
title: "Rsyslog #01: Configurando um Servidor de Log Básico"
layout: post
date: "2021-10-05"
tags:
  - rsyslog
  - linux
  - log
star: false
category: blog
author: keven
description: "Tive a oportunidade de trabalhar com Rsyslog por um bom tempo, auxiliando na centralização dos logs de servidores. Isso me permitiu ver a necessidade de termos logs centralizado, bem como o quão importante eles são para um sistema.

Neste artigo, tenho como objetivo ensinar a como configurar um servidor de log básico utilizando a ferramenta Rsyslog e introduzir um pouco sobre Facilidade e Nível.

Essa será uma série de artigos, sendo este o primeiro. Os demais, serão mais específicos da ferramenta.

Espero que gostem!"
hidden: false
---

_As mensagens em uma rede de computadores podem ser centralizadas em um único servidor. Quando centralizadas, fica mais fácil o gerenciamento, análise e solução de problemas relacionados as máquinas presentes nesta rede. O artigo a seguir, tem como objetivo ensinar a como configurar um servidor de log básico utilizando essa ferramenta, e introduzir ao leitor conceitos de Facilidade e Nível._

## >_ O que é um servidor de Log?

Basicamente, um servidor de log, seria uma máquina que centraliza todas as mensagens de algumas máquinas em uma rede, para que sejam armazenadas, e posteriormente, com outras ferramentas, analisadas. A necessidade de um servidor centralizado de Log, é essencial, quando temos mais de um (diria 2 ou mais) servidores. Além de manter os dados em um outro servidor, garantindo assim uma certa dificuldade de alterá-los caso haja um incidente de segurança da informação, ele também permite a centralização dos logs de diversos servidores, que poderão ser analisados em um único local.

Imagine você tendo que cuidar de mais de 500 servidores... Ficaria difícil, em algumas situações, identificar algum problema, sem um servidor de log centralizado, não é mesmo?

## >_ Qual servidor de Log devo utilizar?
Existem inúmeras alternativas no mercado, como por exemplo syslog-ng, rsyslog, logstash, e até mesmo, se formos pra nuvem, o CloudWatch da AWS, dentre outros. Neste artigo, meu foco será introduzi-lo ao Rsyslog. Entretanto, nada impede que você, leitor, utilize outra ferramenta. Apenas devo informar que, provavelmente, o artigo não servirá para a ferramenta escolhida, caso a ferramenta escolhida seja diferente.

## >_ Considerações
Como forma de melhorar o entendimento, estarei disponibilizando um arquivo Vagrantfile no fim do artigo que possibilitará a criação de um ambiente de estudo. Os requisitos para o artigo, no entanto, serão apenas duas máquinas, sendo uma que servirá de **Cliente** e outra como **Servidor**. Utilizarei Centos/7 em ambas as duas, por familiaridade. Caso tenha uma familiaridade com outra distribuição Linux, fique a vontade de utilizá-la. Não serão utilizadas máquinas Windows.

Com essas considerações, vamos a mão na massa!

## >_ Criando o ambiente de estudo
Caso tenha optado por utilizar o arquivo Vagrantfile, fornecido no artigo para criar seu ambiente de estudo, apenas execute o comando abaixo para criar o ambiente:

{% highlight shell %}
vagrant up
{% endhighlight %}

O processo de criação demora um certo tempo, até que todas as máquinas sejam criadas. Após a criação, para acessar as máquinas, execute os comandos abaixo:
  
{% highlight shell %}
vagrant ssh server # ---> Entra na máquina do servidor
vagrant ssh client # ---> Entra na máquina do cliente
{% endhighlight %}

## >_ Instalando o Rsyslog
Por padrão, as máquinas Centos/7 já possuem o Rsyslog instalado. Entretanto, ele é de algumas versões anteriores do mesmo. Para ver a versão do Rsyslog na máquina **Cliente** e **Servidor**, utilize o comando abaixo:

{% highlight shell %}
rsyslogd -v
{% endhighlight %}

![Imagem][1]

A versão mais recente do Rsyslog, no momento que escrevo este artigo, é a 8.2106.0. Olhando no site do Rsyslog, notamos que a versão 8.24, é de outubro de 2017. Em um próximo artigo será introduzida a linguagem RainerScript, por isso é necessário que uma versão mais atualizada esteja presente, evitando assim, possíveis problemas de incompatibilidade com a linguagem.

Vamos então incluir o repositório oficial do Rsyslog, para conseguir um binário pré-compilado atualizado. Para isso, basta executar os seguintes comandos abaixo com o usuário **root** em ambas as máquinas (**Servidor** e **Cliente**):

{% highlight shell %}
cd /etc/yum.repos.d/
wget http://rpms.adiscon.com/v8-stable/rsyslog.repo
yum install rsyslog -y
{% endhighlight %}

Após a execução dos comandos, teremos a versão mais atualizada do Rsyslog (versão estável)

![Imagem][2]

## >_ Subindo servidor Rsyslog (legado)
Com a versão mais recente do programa instalada, podemos agora criar o nosso **Servidor**. É possível criar um **Servidor** rsyslog de forma muito simples, entretanto, legada. Ela utiliza a forma anterior ao qual o Rsyslog trabalhava seus arquivos de configuração. O arquivo de configuração padrão do Rsyslog, é o arquivo presente em _/etc/rsyslog.conf_.

No diretório _/etc/rsyslog.d/_ ficam os arquivos que são importados pelo rsyslog. Nele que você pode configurar regras personalizadas. Uma boa prática, é criar seus _rulesets_ personalizados neste diretório, ao invés de criar no arquivo _rsyslog.conf_.

Vamos configurar primeiramente nosso **Servidor**. Pra isso, iremos remover o comentário das seguintes linhas:

![Imagem][3]

Salve o arquivo, e execute o comando abaixo:

{% highlight shell %}
systemctl restart rsyslogd
{% endhighlight %}

Isso fará com que o Servidor seja reiniciado, e com isso, carregue o arquivo de configuração novamente. Dessa forma, ele saberá que deve escutar na porta 514 - como define a linha onde está presente $UDPServerRun 514 - e que deve ser pelo protocolo UDP.

Certo. Mas será que ele está funcionando? Será que está recebendo log? Bom, precisamos sabermos isso. Primeiramente, vamos verificar se o **Servidor** está funcionando corretamente. Digite em seu terminal:

{% highlight shell %}
systemctl status rsyslogd
{% endhighlight %}

Está com status ativo? Está rodando? Ótimo. Já sabemos que o Servidor está sendo executado. Agora precisamos verificar se a porta no protocolo UDP está realmente aberta, executando o comando abaixo:

{% highlight shell %}
netstat -lnup | grep 514
{% endhighlight %}

Apareceu algum resultado? Se apareceu um resultado, temos agora um **Servidor** de Logs configurado.

Mas, calma aí... Não temos a certeza ainda que está funcionando. Isso porque o **Servidor** ainda não está coletando arquivo de logs de outros lugares, apenas dele mesmo. Precisamos configurar agora a máquina **Cliente**, para enviar logs para o servidor.

_PS:. Antes de deixarmos o **Servidor** de lado por um momento, e configurarmos a máquina **Cliente**, execute o comando abaixo, e anote o endereço IP da máquina **Servidor**:_

![Imagem][4]

_Mais tarde, enquanto estivermos configurando a máquina **Cliente**, precisaremos especificar para onde estaremos enviando os logs, e iremos informar que será para este endereço IP._

## >_ Configurando cliente Rsyslog (Legado)
Com o **Servidor** já configurado, vamos então para a máquina **Cliente**. Acesse ela em outra aba do terminal, e abra o arquivo de configuração em _/etc/rsyslog_.conf. No fim do arquivo, adicione a seguinte linha de código:


{% highlight shell %}
*.* @<ENDEREÇO_IP>
{% endhighlight %}

PS:. Substitua o <ENDEREÇO_IP>, pelo endereço IP do Servidor ao qual você anotou no passo anterior.

## >_ Testando configuração de comunicação Cliente-Servidor
Precisamos testar agora se o **Cliente** está enviando os logs para o **Servidor** centralizado. Salve o arquivo, e saia dele, caso ainda não tenha feito. O próximo passo é reiniciar o serviço rsyslog da máquina **Cliente**, utilizando o mesmo comando que usamos anteriormente para reiniciar o serviço na máquina **Servidor**:

{% highlight shell %}
systemctl restart rsyslogd
{% endhighlight %}

Reiniciou? Vamos verificar se o serviço está funcionando, executando o comando abaixo:

{% highlight shell %}
systemctl status rsyslogd
{% endhighlight %}

Está ativo? Se estiver, então a nova linha que adicionamos no arquivo de configuração do Rsyslog foi carregada pelo serviço. Os últimos passos, para verificar se o **Servidor** está funcionando seriam: executar um comando para ler um dos arquivo de log, neste caso, o _/var/log/messages_, e também, gerarmos um log de exemplo na máquina **Cliente**, para verificar se está sendo enviado para o **Servidor**.

Execute o comando abaixo no **Servidor**:
{% highlight shell %}
tail -f -n1 /etc/messages
{% endhighlight %}

Esse comando está mostrando de forma iterativa no terminal, toda novidade que for registrada no arquivo /etc/messages, dentro do **Servidor**.

Na máquina **Cliente**, execute o comando abaixo:
{% highlight shell %}
logger -p info Mensagem de teste
{% endhighlight %}

Com esse comando, enviamos um log de teste para o serviço do Rsyslog, que irá salvar em algum dos arquivos ao qual nele foi dito para salvar. No nosso caso, ele irá enviar também para a máquina **Servidor**, como definimos adicionando aquela linha, no arquivo de configuração.

Volte agora para a máquina **Servidor** e verifique se chegou alguma mensagem, como a mensagem abaixo. Se chegou, então a máquina **Servidor** e a máquina **Cliente** estão conseguindo se comunicar corretamente.

![Imagem][5]

## >_ Entendendo configuração da máquina Cliente e Servidor

### Servidor
Na máquina **Servidor**, descometamos a linha que permitia com que fosse possível o recebimento de arquivos de log via rede. Nós abrimos a porta 514 do protocolo UDP, para que a velocidade de entrega do log fosse maior. Entretanto, com isso, podemos perder alguns logs, já que não existe o *three handshake* presente no protocolo TCP. Aconselho utilizar o protocolo UDP apenas para quando você prioriza mais a velocidade de entrega, do que ter a garantia de recebimento da mensagem. Caso queira garantir a entrega da mensagem de log, use o TCP.

O TCP, apesar de garantir a entrega, acaba sendo mais lento. E no caso de queda do **Servidor de logs**, ele vai tentar garantir o envio da mensagem ao destino mesmo assim. Então ele vai ficar tentando enviar a mensagem, e tentando, e tentando... e tentando...

É possível também utilizar certificados TLS, junto com o protocolo TCP, o que permite com que sua mensagem não seja lida por terceiros, caso seja interceptada antes de chegar no **Servidor**. Entretanto, isso faz com que a velocidade acabe sendo mais prejudicada.

Uma alternativa para garantir a entrega da mensagem ao **Servidor**, uma velocidade maior que o módulo do _imtcp_, e também caso não necessite de um certificado TLS, é a utilização do módulo _imptcp_. Ele é mais simples que o módulo _imtcp_, mas não permite a criptografia por ele, tendo que ser realizada pelo software _stunnel_ caso necessite. Neste último caso, em específico, utilize o _imtcp_.

### Cliente

Na máquina **Cliente**, definimos qual seria o protocolo utilizado colocando apenas um "@" na frente do endereço IP do **Servidor** no arquivo de configuração. Isso definiu o protocolo de envio como UDP. Caso desejássemos utilizar o protocolo TCP, seriam dois "@" seguidos do endereço IP.

O *.*, definiu a facilidade e nível do log, em cada "*", respectivamente. Isso diz que todos os logs de todos os níveis e facilidades são enviados ao **Servidor de log**. Poderíamos limitar que tipo de log seria enviado ao **Servidor de log**, através da facilidade, fazendo o seguinte, por exemplo:

{% highlight shell %}
kern.*  @<IP-DO-SERVIDOR>
{% endhighlight %}

Agora, apenas os logs do kernel serão enviados para o **Servidor**.

Se quisermos, podemos também definir apenas um nível mais severo de log do kernel, para apenas logs de erros, ou mais críticos que isso, sejam enviados ao **Servidor**:

{% highlight shell %}
kern.err  @<IP-DO-SERVIDOR> 
{% endhighlight %}

Agora, você receberá apenas logs de erro mais severos, como Error, Critical, Alert, e Emergency.

## >_ Conclusão
Esse primeiro artigo sobre o Rsyslog, mostrou de uma forma simples, como criar um servidor de log centralizado. Entretanto, há muita coisa que podemos melhorar. Nos próximos artigos, irei me aprofundar mais no seu funcionamento, tipos de módulos existentes, entre outras análises, que em um artigo só, fica difícil escrever.

Gostou do artigo, tem alguma dúvida ou possui alguma sugestão para os próximos? Por favor, comente! Feedbacks me auxiliam a melhorar o conteúdo por aqui.

---

## Links
* Vagrantfile: [GitHub](https://gist.github.com/stephan-lopes/3213f88f44094ed8f25d5bfc328c9a5a)
* Rsyslog Facilities and Levels: [GitHub](https://wiki.archlinux.org/title/rsyslog)

<div class="breaker"></div>

[1]: {{ site.url }}/assets/images/rsyslog-01-configurando-um-servidor-de-log-básico/1628350418210.png

[2]: {{ site.url }}/assets/images/rsyslog-01-configurando-um-servidor-de-log-básico/1628352331901.png

[3]: {{ site.url }}/assets/images/rsyslog-01-configurando-um-servidor-de-log-básico/1628385769857.png

[4]: {{ site.url }}/assets/images/rsyslog-01-configurando-um-servidor-de-log-básico/1628909014687.png


[5]: {{ site.url }}/assets/images/rsyslog-01-configurando-um-servidor-de-log-básico/1629071777012.png
