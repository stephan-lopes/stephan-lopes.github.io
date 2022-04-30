---
title: "Assinatura de Commits com GPG"
layout: post
date: 2022-04-29 23:00
tag:
    - gpg
    - git
    - github
    - guia-rapido
star: false
category: blog
author: keven
description: "Como configurar o GPG para assinar commits do Github"
hidden: false
---

*No Git, toda vez que é feito um commit, ele coleta seu usuário e seu e-mail, e anexa em um Commit. Entretanto, ele pode ser manipulado, e alguém pode se passar por você. Sendo assim, o ideal é assinar seus commits com chave GPG. Com ela, você consegue comprovar que você é você, para todos aqueles que possuem uma chave pública da chave privada que só você possuí. Este tutorial, é um guia de configuração, bem como a demonstração do problema em si, e em como resolvê-lo. Espero que gostem, e coloquem em prática as dicas!*

## >_ O Problema do Commit Sem Assinatura
Em um ambiente de produção, o Commit sem assinatura é muito mais comum que o Commit com assinatura. Por isso, é importante que você configure o GPG para assinar seus commits. Essa, digamos, má pratica, abre brechas que podem ser exploradas, caso seus commits não sejam assinados. Vou mostrar um exemplo.

![Imagem][1]

Quando adiciono um arquivo em um projeto e faço um *git commit*, o git não assina o commit por padrão. Dessa forma, ele acaba pegando a variável **user.name** e **user.email**, que são definidas com os seguintes comandos:

{% highlight bash %}
git config --global user.name "Seu Nome"
git config --global user.email "email@teste.com"
{% endhighlight %}

Quando enviado por push, ele não valida se essas informações são verdadeiras ou não. Ou seja, quem vê dentro de um repositório, não sabe se realmente foi aquela pessoa que fez aquele commit. 

Pra demonstrar isso, vamos criar um arquivo, e fazer um commit com outro usuário e e-mail, usando o seguinte código para isso:

{% highlight bash %}
git commit --author="Mark Zuckerberg marquinho@teste.com" -m "Teste de commit"
{% endhighlight %} 

Após confirmarmos o Commit, temos esse resultado:

![Imagem][2]

Talvez você diga: *"Mas Keven... Isso é em ambiente local, não vai acontecer nada se você colocar ele em um repositório do github."*

Se pensou isso, sinto lhe dizer, mas está enganado. Olha o que acontece quando envio por push esses commits:

![Imagem][3]

Preocupante, não? Mas calma, vamos resolver isso.

## >_ Gerando uma Chave GPG
O primeiro passo para conseguirmos assinar nossos commits, é gerar uma chave GPG. Tenha certeza de que a sua máquina tem o GPG instalado. Usaremos a **versão 2.2.19**, nesse tutorial, mas você pode usar qualquer versão (preferencialmente >= 2).

Para instalar, vamos usar o comando abaixo em um Ubuntu linux (em outro SO, como Windows ou Unix, sugiro procurar na documentação do github):

{% highlight bash %}
sudo apt install gnupg
{% endhighlight %}

Após a instalação, vamos criar uma chave GPG, utilizando o comando abaixo:

{% highlight bash %}
gpg --full-generate-key
{% endhighlight %}

Após a execução do comando, ele irá realizar a geração de uma chave GPG. Selecione o tipo de chave (no tutorial usaremos a **RSA and RSA**), e dê enter.

Feito isso, será perguntado o tamanho da chave em bits. Para uma maior segurança, usaremo o tamanho de **4096 bits**.

Será então solicitado que informe o número de dias que a chave deve permanecer viva. Se você não informar nada, a chave ficará viva por 0 dias, ou seja, para sempre. No tutorial, usaremos uma chave com validade de **0 dias**. Nesse caso, quando é definido 0 dias, irá ser questionado se tem realmente certeza que quer criar uma chave com uma validade tão longa. **Confirme e dê enter**.

Por fim, insira o **Nome**, e o **endereço de e-mail** que deseja vincular a chave. O endereço de e-mail, pode ser de dois tipos: 
- Um endereço de e-mail pessoal, registrado na sua conta do GitHub.
- Ou, o mais indicado, um endereço de e-mail no-reply, que é gerado pelo GitHub.

Caso opte pela segunda opção, você pode entrar no site do GitHub e verificar este endereço de e-mail no-reply. Pra fazer isso, em seu **Perfil**, acesse **Settings**, posteriormente, acesse **Emails**, e por fim, marque a caixa onde está escrito **Keep my email addresses private**. Dentro dela, você pode obter esse endereço de e-mail no-reply.


![Imagem][4]

Este endereço, dependendo de quando criou sua conta, pode mudar. Quem criou em 2017, pode ter um endereço de e-mail no-reply diferente, sem aqueles números antes do **+**. Essa é a forma mais indicada, para evitar que deixe exposto seu e-mail, publicamente. Em casos que deseja deixar exposto, a primeira opção será a mais ideal. 

No meu caso, o e-mail no-reply é **40838038+stephan-lopes@users.noreply.github.com**. Após informar o endereço de e-mail, você pode adicionar algum comentário, ou **deixar em branco**.

Será então solicitado para confirmar. Para isso, digite a letra **o** e dê enter. Depois disso, você precisará informar a **Passphrase**, que seria como uma senha. Informe a **Passphrase** e dê enter. Use o mouse, ou o teclado, para que a chave seja gerada, e pronto! Ela foi gerada e agora está em seu ambiente!

## >_ Colocando a Chave Publica na Conta do GitHub
Com a chave gerada, precisamos inserir ela no Github. Precisamos fazer isso, por que o GitHub precisa validar se a chave realmente é sua. Se não, nada impediria você também de gerar uma chave, como fizemos anteriormente, informando um e-mail, que não é seu, e, se passando por essa pessoa, fazer um commit.

Vamos precisar da chave pública, então, utilize o comando abaixo:

{% highlight bash %}
gpg --list-secret-keys --keyid-format=long
{% endhighlight %}

Esse comando listará todas as chaves que estão armazenados no agent. Você precisará encontrar uma chave que tenha o **keyid** que você gerou. Esse **keyid**, seria o código de letras depois do **rsa4096/**, por exemplo.

![Imagem][5]

Copie esse código, e execute o seguinte comando abaixo, para ter acesso a chave pública:
    
{% highlight bash %}
gpg --armor --export FF79A185C539E72E
{% endhighlight %}

Copie a chave pública, fornecida como saída do comando e acesse o GitHub. Na página de configuração da conta, na seção **SSH and GPG Key**, e depois, na seção **GPG Key**, clique em **New GPG Key**. **Coloque a chave pública**, e depois, clique em **Add GPG Key**. Forneça sua senha do GitHub para concluir.

## >_ Configurando Endereço de E-mail Localmente
Agora que a chave foi inserida no GitHub, precisamos configurar o endereço de e-mail localmente. Lembra do endereço de e-mail no-reply que você gerou? Este endereço é o endereço que será usado aqui, e será atrelado aos seus commits.

Para concluir, use o seguinte comando:

{% highlight bash %}
git config --global user.email "COLOQUE AQUI SEU ENDEREÇO DE EMAIL AQUI"
{% endhighlight %}

Isso irá mudar globalmente. Se você quiser mudar apenas para o repositório atual, use o comando sem a opção **--global**.

## >_ Configurando Assinatura em Ambiente Local
Mesmo com todas essas configurações, a ferramenta git ainda não sabe que deve assinar todos os commits. Lembra do que eu disse no inicio, em que isso não é feito por padrão? 

Caso queira assinar um commit, até o momento, você teria que adicionar a flag **-S** no comando *git commit*. 

{% highlight bash %}
git commit -S -m "Mensagem de commit"
{% endhighlight %}

Entretanto, essa forma é uma forma um pouco "custosa". Isso, porque, a cada vez que precise fazer um commit, terá que lembrar de adicionar a Flag. Outro problema que podemos identificar também, é que não definimos para a ferramenta, qual chave ela deve utilizar. Imagine que tenhamos 10 chaves GPG. Como o git saberá qual chave deve ser usada?

E os problemas não acabaram por ai... Como o GPG irá perguntar a palavra passe? Por padrão, ele não sabe para qual tty ele deve perguntar isso!

Então, mesmo tentando fazer essa assinatura manualmente, você iria se deparar com o seguinte erro:

{% highlight bash %}
$ git commit -S -m "...."
error: gpg failed to sign the data
fatal: failed to write commit object
{% endhighlight %}

Precisamos resolver esses problemas. Vamos primeiro informar o git sobre qual a chave que deve ser utilizada. Busque o **keyid**, como feito anteriormente:

{% highlight bash %}
gpg --list-secret-keys --keyid-format=long
{% endhighlight %}

E depois, informe ele no seguinte comando (*Troque o FF79A185C539E72E pelo seu keyid.*):

{% highlight bash %}
git config --global user.signingkey FF79A185C539E72E
{% endhighlight %}

Feito! Agora, todos os commits assinados que você fizer, serão assinados com a chave que você definiu! 

Entretanto, o git ainda não faz isso de forma automática. Ele ainda espera que você informe a flag **-S** no comando *git commit*. Para mudar isso, use o comando abaixo:

{% highlight bash %}
git config --global commit.gpgsign true
{% endhighlight %}

Com isso, cada vez que executar um *git commit*, o git irá assinar o commit de forma automática, sem você precisar adicionar a flag **-S**.

O último passo, é definir nosso TTY, para que o GPG possa perguntar a palavra passe. Caso contrário, teremos aquele erro na hora que formos assinar o commit, por não fornecermos a palavra passe. Execute o comando abaixo, em seu **~/.bashrc**:

{% highlight bash %}
echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
{% endhighlight %}

Caso esteja usando outro terminal que não seja o bash, use o arquivo apropriado para ele. Uma outra observação a ser feita, é em casos de uso com o zsh, utilizando fontes Powerlevel10k. Copie o export acima para o arquivo .zshrc, antes do Powerlevel. Caso contrário, isso não irá funcionar.

Abra uma nova sessão, ou então, execute o comando abaixo no terminal, para que isso já se torne válido na sessão atual:

{% highlight bash %}
source ~/.bashrc
{% endhighlight %}

Por fim, reinicie o agent, com o seguinte comando:
    
{% highlight bash %}
gpgconf --kill gpg-agent
{% endhighlight %}

Pronto! A partir de agora, todo o commit que enviar no git, será assinado com a chave que você definiu, de forma automática!

## >_ Enviando um Commit para o GitHub
Vamos agora enviar um commit para o Github, para testar se tudo está funcionando corretamente. Pra isso, eu criei em meu repositório um novo arquivo, chamado **teste-commit.txt**, adicionei ele, executando o comando *git add*, e depois, executei o comando *git commit*. Por fim, fiz o *git push*.

{% highlight bash %}
git add teste-commit.txt
git commit -m "Adicionando arquivo teste-commit.txt"
git push origin main
{% endhighlight %}

Quando fizer o *git commit*, será solicitado para colocar a palavra passe da chave GPG, para que possa ser assinado esse commit. Após realizar os comandos acima, temos este resultado:

![Imagem][6]

Veja que com isso, temos um commit assinado! Podemos ver isso, com a badge de **Verified** nele. No caso de estar **Unverified**, semelhante com o que aparece abaixo, verifique seu commit, e veja se o nome de usuário, o email e a chave GPG estão corretos. Verifique também se colocou a chave GPG Pública corretamente no Github.

![Imagem][7]

## >_ Definindo Cache de Chaves GPG
Embora tenhamos definido uma forma com que todos os commits sejam assinados, teremos que colocar a passphrase todas as vezes que fizermos um commit. Por mais que isso dê mais segurança, para algumas pessoas pode ser um problema, pois, a cada vez que fizermos um commit, o git irá pedir a passphrase.

Podemos então, definir um cache, fazendo com que o agent armazene temporariamente a passphrase. No tutorial, iremos definir esse período em 1 hora, mas, como você poderá ver, podemos definir qualquer período. 

Para realizar essa tarefa, precisamos criar um arquivo chamado **~/.gnupg/gpg-agent.conf**. E dentro dele, definir o seguinte:

{% highlight bash %}
default-cache-ttl 3600
max-cache-ttl 3600
{% endhighlight %}

Dessa forma, ele irá armazenar a passphrase, depois de digitada, por uma hora. Após o prazo, o agent irá pedir a passphrase novamente.

## >_ Assinando Commits Antigos
Mostramos como faz para assinar os novos Commits, mas, e caso queira assinar commits antigos, como podemos fazer isso?

Uma forma que você pode fazer isso, é utilizando o comando **git rebase**. Com esse comando, ele irá assinar os commits não assinados. Lembre-se que o e-mail deve ser o mesmo da chave, sendo assim, em alguns casos, será necessário alterar o e-mail também.

Abaixo, vamos fazer um rebase, para que o git assine os commits antigos e altere o e-mail, de todos os commits. Entretanto, o código abaixo serve como um exemplo pro tutorial, e não algo a ser seguido, até porque ele modifica todas as datas, não mantendo as datas antigas.

{% highlight bash %}
git rebase --exec 'git commit --amend --author="stephan-lopes <40838038+stephan-lopes@users.noreply.github.com>" --no-edit -S -n' --root
{% endhighlight %}

Com esse comando, ele irá modificar o autor do commit, e assinar ele. Talvez não seja a melhor forma de se fazer isso, para o seu caso. Estou apenas demonstrando que é possível fazer essa assinatura de commits anteriores. 

## >_ Bloquear Pushes que exponham o e-mail público

Pode acontecer de você acabar em algum momento, definindo seu e-mail, e acabar esquecendo de mudar para o e-mail no-reply. Para evitar que faça um push e exponha ele, existe uma configuração no GitHub, que permite impedir isso.

No seu perfil, acesse **Settings**, posteriormente, acesse **Emails**, e por fim, marque a caixa onde está escrito **Block command line pushes that expose my email**.

Com isso, toda vez que você tentar enviar um push, expondo o seu endereço de e-mail público, será impedido.

## >_ Modo Vigilante
É possível adicionar o Modo Vigilante. Isso faz com que todos os seus commits, tanto os atuais, como os anteriores, mostrem se você é realmente você. Se o Commit não for assinado, ou está assinado, mas a assinatura é completamente diferente, o commit é definido como **Unverified**. Se o nome do Autor é diferente, mas o e-mail é igual, ele mostra **Partially Verified**. Se ele estiver com e-mail e usuário iguais o da assinatura, ele mostra **Verified**.

Para ativar o modo vigilante, no seu perfil, acesse **Settings**, posteriormente, acesse **SSH and GPG Keys**, e por fim, na seção de **Vigilant Mode** marque a caixa onde está escrito **Flag unsigned commits as unverified**.

## >_ Conclusão
Neste tutorial/artigo, quis demonstrar um pouco mais sobre o problema de não assinar seus commits, mostrar como assinar commits, e como fazer um rebase para assinar commits antigos, caso opte por isso. Além disso, foi ensinado um pouco sobre geração de chave GPG, como configurar cache das chaves, e uma forma de não expor seu e-mail pessoal, mas sim um gerado pelo Github, para ter maior segurança em seus commits.

Também foi ensinado algumas formas de melhorar a indentificação, utilizando o modo vigilante, e como impedir que seu endereço de e-mail seja exposto sem querer, em algum commit, quando usar o push. Espero que tenha gostado, e que esse artigo tenha sido útil. Caso você tenha ainda mais interesse em me ajudar, curta e compartilhe o artigo, além de interagir com sugestões, dúvidas, e comentários, na seção de comentários abaixo.

<div class="breaker"></div>

[1]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/5496900581142.png

[2]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/6636094248085.png

[3]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/9114529757421.png

[4]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/6096521027427.png

[5]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/8207360680388.png

[6]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/1311245618908.png

[7]: {{ site.url }}/assets/images/assinatura-de-commits-com-gpg/2013428650008.png