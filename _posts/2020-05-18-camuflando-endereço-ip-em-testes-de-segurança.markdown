---
title: "Camuflando Endereço IP em Testes de Segurança"
layout: post
date: 2020-05-18 00:01
tag:
  - anonsurf
  - protonvpn
  - security
star: false
category: blog
author: keven
description: "Neste artigo, conto um pouco da minha experiência com o teste de duas ferramentas que podem ser usadas para camuflar seu endereço IP em testes de segurança. Espero que gostem do artigo, e que ele inspire a utilizar ambas as ferramentas em seus testes."
hidden: false
---

## Introdução

Recentemente, estava procurando formas de camuflar o endereço IP, sem utilizar Whonix + Tor, ou Proxychains + Tor, que são dois métodos que podem ser utilizados para este objetivo. Estava vendo formas alternativas, devido a ambos os métodos, apesar de possuírem uma forma de camuflar o IP utilizando-se da rede Tor, acabam obrigando a fazer uma série de configurações (Whonix, por exemplo, precisa ser usado dentro de uma Maquina Virtual, e dependendo do uso, precisa ser configurado como Gateway), ou possuem algumas limitações (Proxychains, permite apenas abrir programas utilizando-se desse beneficio de utilizar o IP da rede Tor por padrão. E mesmo assim, ele acaba também limitando a funcionalidade de alguns programas).

Dessa forma, procurando, achei duas alternativas: uma não muito usual, utilizando VPNs do ProtonVPN, e outra mais fácil de se utilizar em testes de segurança, e também mais conhecida, utilizando a rede Tor para essa finalidade.

A seguir, estarei mostrando essas duas alternativas, assim como os testes realizados com ambas. Estarei disponibilizando no final do artigo, o endereço do repositório no GitHub, onde contêm o arquivo Vagrantfile, para caso queira realizar os testes em seu computador.

## ProtonVPN

Uma das alternativa, é a utilização de uma VPN. Pode ser de qualquer provedor de VPN. Nesse artigo, me limito ao provedor ProtonVPN em sua versão gratuita, por conta da ferramenta que mostrarei logo em seguida. Lembrando que o artigo destina-se a testes de segurança autorizados (legalizados). Deixo bem claro isso, pois, utilizar esse método, gera rastro do seu IP real na VPN. Entretanto, a utilização da VPN, possibilita camuflar seu IP real em um ataque autorizado.

### Configuração e Utilização

Para utilizar a alternativa acima, é necessário antes de tudo, que crie uma conta no site da ProtonVPN. Eles possuem alguns planos disponíveis, que vão de acordo com sua necessidade. Foi utilizada a versão gratuita para os testes.

![Imagem][1]

Após a criação e autenticação da conta, será necessário efetuar download do cliente para o sistema operacional em que deseja utilizá-lo. No caso desse artigo, foi utilizado o cliente para GNU/Linux, para distribuições Debian.

![Imagem][2]

Quando tiver terminado a instalação, é necessário efetuar configurações no cliente instalado. Para isso, digite no terminal:

{% highlight shell %}
sudo protonvpn init
{% endhighlight %}

Com isso, você precisará entrar com as credenciais de acesso fornecidas na página de **Conta**, na seção de **Nome de Usuário OpenVPN / IKEv2**. Ele irá pedir para escolher um plano.

Como o cliente não me impediu de escolher, escolhi o plano Visionary, mesmo tendo o plano Free. Além disso, escolhi o protocolo UDP, entretanto, posteriormente durante os testes, mudei para TCP. Após isso, sua VPN já está configurada.

![Imagem][3]

Para conectar a VPN, basta digitar no terminal:

{% highlight shell %}
sudo protonvpn connect
{% endhighlight %}

Então, serão dadas alguma opções, onde você poderá escolher a localidade, bem como, em qual servidor que deseja entrar.

![Imagem][4]

Fiz um teste, utilizando a ferramenta curl, onde verifiquei o meu endereço IP antes e depois de conectar na vpn. Criei um alias para ele em meu ~/.bashrc, chamado **myip**. Durante o artigo, quando ver esse comando digitado no terminal, saiba que ele apenas executa o comando abaixo:

{% highlight shell %}
curl http://ipecho.net/plain; echo
{% endhighlight %}

A imagem abaixo, mostra que se conectou a VPN

![Imagem][5]

### Utilizando em Testes de Segurança em Aplicações

Com tudo configurado, modifiquei alguns parâmetros para testar sua funcionalidade em uma simulação de um ambientes real. Durante o *protonvpn connect*, selecionei a opção de fazer a conexão via TCP ao invés de UDP. Não vou me atentar a explicar o motivo em detalhes, mas como foi simulado um ataque de tentativas de login em um servidor SSH, o método de conexão que precisava ser utilizado no ProtonVPN seria TCP.

Criei uma instância de VM no GCP, com uma imagem Ubuntu 16.04 LTS. Por padrão, a máquina já vem com algumas configurações do SSHGuard, que é quem vai escutar o arquivo de log, auth.log, e bloquear o endereço IP após algumas tentativas erradas de acesso. Como deixei a configuração por padrão, duas tentativas já são suficientes para bloquear o endereço IP no firewall.

O resultado do teste foi que, após duas tentativas erradas de acesso, ele bloqueou o endereço IP e na terceira tentativa, recebeu timeout. Isso aconteceu, por uma regra ter sido adicionada no Firewall pelo SSHGuard. Em outras palavras, ele não muda automaticamente o seu endereço IP, em casos de bloqueio pelo host, tendo que fazê-lo manualmente.

![Imagem][6]

Para mudar seu endereço IP manualmente, e randomicamente, sem ter que selecionar na lista, como feito anteriormente, basta executar o comando abaixo:

{% highlight shell %}
sudo protonvpn connect -r -p tcp
{% endhighlight %}

Fazendo isso, você terá um novo endereço IP, ao qual poderá continuar com seus testes de segurança, sem ter que expor seu endereço de IP real.

![Imagem][7]

### Gerando novo endereço IP a cada 10 segundos

Talvez você se pergunte: "E se eu quiser automatizar essa mudança, e ficar, constantemente, mudando o endereço IP? Como eu poderia fazer isso?". Existe uma ferramenta chamada IPChange. Ela faz isso de forma automatizada, utilizando ProtonVPN, tendo que apenas especificar o tempo que deseja que seja renovado o endereço IP. O repositório do GitHub para ela, encontra-se no final do artigo.

Não vou entrar em detalhes em como a ferramenta funciona, nem em seu uso. Estarei mostrando a seguir como fazer isso sem essa ferramenta, e deixando como um processo em segundo plano.

O comando abaixo, faz isso de forma automatizada, tendo apenas que mudar o tempo (em segundos) após o comando sleep. Isso se quiser alterar o tempo entre as mudanças de endereço IP:

{% highlight shell %}
while :; do sudo protonvpn connect -r -p tcp; sleep 10; done &>/dev/null & alias killchange="kill -9 $!"
{% endhighlight %}

Caso queira parar o processo, basta digital killchange:

{% highlight shell %}
killchange
{% endhighlight %}

Porém, temos um problema. O processo de buscar um novo endereço de IP na ProtonVPN, acontece da seguinte forma: Você está utilizando o endereço IP "X" da VPN, e solicita para mudar de endereço IP. Então, o cliente do ProtonVPN desconecta você, fazendo-o voltar ao seu IP real. Logo em seguida, ele tenta se conectar a outra localidade. E quando conectado, lhe entrega para uso, o endereço IP "Y".

Talvez se pergunte: "Qual o problema do que foi descrito acima?". O problema consiste em que, você fica um período de tempo, mesmo que muito curto, com seu endereço IP real. Em um teste de segurança, por exemplo, isso pode acabar gerando logs com seu endereço IP real, fazendo o uso da ferramenta, desnecessário, caso não queira que isso aconteça. O tempo em que você volta para o seu IP real até se conectar novamente na VPN, consiste no seguinte cálculo:

> #### *(Tempo com endereço IP real) = (Tempo de Desconexão) + (Tempo de Conexão).*

Executei no terminal, um comando, para que continuamente, verificasse o endereço IP. Quando ele "travava", eu sabia que era por conta de o comando myip ter sido executado na troca de IP. Por isso, eu acabei parando e executando-o novamente. Na imagem abaixo, mostro isso e, descrevo qual é o endereço IP da VPN e qual é o endereço IP real.

![Imagem][8]

Sendo assim, é necessária uma boa conexão, e se atentar para esse problema sabendo que ele pode acontecer. Colocar um tempo maior no comando *sleep*, e utilizar o endereço IP com menos frequência durante um ataque, são algumas alternativas que podem ajudar a contornar esse problema.

## AnonSurf

A outra alternativa, seria o AnonSurf. Essa alternativa é popular, por conta de seu uso em distribuições como Kali Linux e Parrot OS. Você pode ver mais sobre o uso da ferramenta e suas outras formas de configuração, no repositório, que estará também no final do artigo.

### Configuração e Utilização

Para configurar, é bem simples: basta clonar o repositório, entrar dentro do diretório e executar o script chamado installer.sh, que irá realizar todo o processo de instalação para você.

{% highlight shell %}
git clone https://github.com/Und3rf10w/kali-anonsurf && cd kali-anonsurf && sudo ./installer.sh
{% endhighlight %}

Pronto, o AnonSurf já está instalado! Caso deseja verificar se foi instalado corretamente, digite no terminal:

{% highlight shell %}
sudo anonsurf
{% endhighlight %}

Caso apresente algumas informações, como na imagem abaixo, ele foi instalado corretamente.

![Imagem][9]

Para iniciar o serviço, digite:

{% highlight shell %}
sudo anonsurf start
{% endhighlight %}

Após isso, acontecerá a conexão:

![Imagem][10]

### Utilizando em Testes de Segurança em Aplicações

Após iniciado e em execução, realizei o mesmo teste que fiz com o ProtonVPN. Em outra máquina do GCP, fiz autenticações inválidas na porta 22 (SSH), na expectativa de que também fizesse o bloqueio do endereço IP. Entretanto, me surpreendi. Após algumas tentativas, notei que quando o endereço IP era bloqueado, o AnonSurf, de alguma forma, "reconhecia" esse fato, e alterava o endereço IP automaticamente.

![Imagem][11]

Dessa forma, não é necessário ficar alterando o endereço IP manualmente durante um ataque, mesmo sendo possível fazer isso. Como reduzi o escopo dos testes, em um ataque de autenticação de em um servidor SSH, é dificil dizer que terá o mesmo resultado em outros tipos de ataque.

Caso deseje mudar manualmente o endereço IP, basta digitar o seguinte comando:

{% highlight shell %}
sudo anonsurf change
{% endhighlight %}

Dessa forma, você terá um novo endereço IP, ao qual poderá utilizar em seus testes.

![Imagem][12]

### Gerando novo endereço IP a cada 10 segundos

Diferente do ProtonVPN, desconheço de algum script, que faça isso para você, como o IPChange faz para o ProtonVPN. Entretanto, o mesmo código (com algumas alterações), utilizado no artigo para o ProtonVPN pode ser usado para o AnonSurf.

O comando abaixo funciona de forma semelhante de como funcionava com o ProtonVPN:

{% highlight shell %}
while :; do sudo anonsurf change; sleep 10; done &>/dev/null & alias killchange="kill -9 $!"
{% endhighlight %}

Caso queira parar o processo, basta digitar *killchange*:

{% highlight shell %}
killchange
{% endhighlight %}

Em meus testes, verifiquei que mesmo com o serviço ligado, não acontecia a mudança tão demorada de IP, como acontece no ProtonVPN. O tempo da desconexão e de conexão é tão rápido, que não consegui notar se o tempo que ficava com o endereço IP real, era o suficiente pra gerar log, ou se então, nem sequer desconectava, mas sim, apenas mudava o endereço IP.

![Imagem][13]

Isso acaba auxiliando nos testes, em comparação com ProtonVPN, devido que, o mesmo acaba não gerando log com o endereço de IP real durante um ataque. Como a mudança de endereço IP é muito rápida, e não gerando log com seu endereço de IP real, é possível mudar o tempo colocando ele para um tempo abaixo dos 10 segundos.

Entretanto, uma coisa que notei durante os testes, foi que durante algumas tentativas de acesso ao servidor SSH o endereço IP que constava no arquivo de log no servidor, não condizia com o endereço IP que listava na minha máquina. Após o bloqueio do primeiro endereço IP pelo servidor, e a mudança automática do mesmo pelo AnonSurf, ele volta a condizer com o endereço IP da máquina nas próximas tentativas de autenticação.

Talvez não seja algo a se preocupar, mas fica uma pergunta no ar: "Se o endereço IP não condiz com o que está sendo utilizado na minha máquina, e eu sei que aquela tentativa de login que consta no log do servidor é minha, como pode essa tentativa chegar no servidor com outro endereço IP?".

## Conclusão

Neste artigo, tive como finalidade mostrar essas duas ferramentas. Elas são alternativas ao Proxychains e Whonix, pra esconder o seu endereço IP real. Não quis me atentar em apenas descrever sobre elas, e dizer suas funcionalidades, mas sim em fazer uma análise dessas duas ferramentas, e em demonstrar tanto o lado positivo, como negativo de ambas, encontrados nos testes.

Ambas as ferramentas conseguem auxiliar um profissional de Segurança da Informação em testes de segurança. Elas conseguem esconder o endereço IP real, o que pode ajudar não ser idêntificado em diversos tipos de ataque. Entretanto, cabe ao profissional escolher qual delas cabe melhor ao uso em seus testes de segurança, podendo o mesmo utilizar de outras possibilidades não descritas no artigo.

Como limitei o escopo do artigo, muita coisa não foi falada sobre IPChange, sobre funcionalidades do ProtonVPN, e também outros usos do AnonSurf, como por exemplo iniciá-lo junto ao sistema.

---

## Repositórios
* Vagrantfile: [GitHub](https://github.com/TheSubmitEquals/protonvpn-anonsurf-test)
* AnonSurf: [GitHub](https://github.com/Und3rf10w/kali-anonsurf)
* IPChange: [GitHub](https://github.com/tuhin1729/IPChange)

<div class="breaker"></div>

[1]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589602859223.png
[2]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589648362557.png
[3]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589649797942.png
[4]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589650323119.png
[5]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589651318732.png
[6]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589656445456.png
[7]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589657377377.png
[8]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589662844516.png
[9]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589664813662.png
[10]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589665078436.png
[11]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589763982157.png
[12]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589764477776.png
[13]: {{ site.url }}/assets/images/camuflando-endereço-ip-em-testes-de-segurança/1589765488796.png