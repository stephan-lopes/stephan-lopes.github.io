---
title: "Vagrant + Kaspersky: Download de Imagens Sem Perder a Segurança"
layout: post
date: 2022-01-24 00:01
tag:
  - vagrant
  - windows
  - kaspersky
  - virtualbox
  - security
  - guia-rapido
star: false
category: blog
author: keven
description: "Se você já lidou com problemas com o Vagrant, relacionado a certificados, este artigo poderá lhe ajudar. Existe uma solução para o problema relacionado ao Vagrant com o Kaspersky, deixando o mínimo possível o dispositivo inseguro nesse processo, impedindo assim, que possíveis ataque possam ocorrer."
hidden: false
---

*O funcionamento do Kaspersky em conjunto de Máquinas Virtuais, ou imagens, criadas através do Vagrant, é um pouco dificultoso. Isso ocorre por uma funcionalidade do Kaspersky que protege sua rede. Neste artigo, explico como contornar esse problema, sem precisar desativar temporariamente o antivírus, ou então, desativar essa função, que tem como foco proteger o usuário.*

## >_ O Problema

O problema ocorre quando tenta conseguir a imagem que não pode ser encontrada localmente no dispositivo onde deseja iniciar o Vagrant. Por exemplo, após executar um vagrant init centos/7 e executar um vagrant up, um erro é mostrado para o usuário:

![Imagem do Problema][1]

Esse tipo de erro impede o Vagrant de efetuar o download da imagem, através do Vagrant Cloud, e posteriormente, do Centos.

## >_ Solução

Para resolver o problema, precisamos entender como funciona essa funcionalidade do Antivírus.

### $ Entendendo o Funcionamento

A Kaspersky Security bloqueia alguns sites de realizarem determinadas tarefas, verificando as conexões criptografadas. Isso é feito para garantir a segurança do usuário.

Isso não quer dizer que os sites tenham vulnerabilidade, ou não sejam seguros. Apenas é o motivo do porque a Kaspersky não permitir a conexão, da forma esperada, com esses servidores.

### $ Resolvendo o Problema

Existem algumas formas de contornar o problema. Uma delas seria **desativar o antivírus durante esse processo**. Dentre todas as alternativas neste artigo, essa é a pior delas. O motivo é simples: Deixa o seu computador ou dispositivo inseguro por um período de tempo. Nesse espaço de tempo, em que ele está inativo, o seu dispositivo pode ser comprometido.

A segunda forma, seria **desativar esse tipo de verificação de conexões criptografadas**, o que seria uma outra forma que pode deixar seu dispositivo inseguro. Caso você acesse um site malicioso, poderá não ser impedido pelo antivírus. Ou se fizer uma conexão de criptografia que ele julgue, quando está ativo, como fraca ou insegura, sua comunicação pode estar correndo um risco de ser comprometida, e/ou interceptada.

A melhor forma, é apenas liberar para alguns domínios específicos, informando em uma lista, os endereços que devem ser ignorados nessa regra. Siga o passo abaixo:

Abra a tela do Kaspersky Security. Após isso, aperte no botão de engrenagem (configurações), e acesse a seção de gerenciamento de redes (Configurações de Rede). Após todos esses passos, busque entre as opões a seção de **Verificação de Conexões Criptografadas**, e busque por um botão de **Endereços Confiáveis**.

![Imagem][2]

Adicione um novo endereço, informando posteriormente o nome de domínio do Vagrant Cloud (vagrantcloud.com), e deixe ele como ativo. Faça o mesmo procedimento com o domínio do Centos. Após concluir os passos, clique em Salvar.

## >_ Conclusão

Quando testar novamente o comando vagrant up, observará que tudo dará certo, sem reclamar de certificado, e, comparado com as outras opções, realizando todo o processo de uma forma mais segura. Caso deseje mais segurança, após o download da imagem você pode desativar os endereços dentro da lista de endereços confiáveis, sem excluir eles. Isso permite que você ative essa regra apenas quando precisar.

<div class="breaker"></div>

[1]: {{ site.url }}/assets/images/vagrant-kaspersky-download-de-imagens-sem-perder-a-segurança/1642996839628.png
[2]: {{ site.url }}/assets/images/vagrant-kaspersky-download-de-imagens-sem-perder-a-segurança/1643003270925.png