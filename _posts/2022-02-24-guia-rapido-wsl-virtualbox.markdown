---
title: "Guia Rápido: WSL + Virtualbox"
layout: post
date: 2021-11-09 00:01
tag:
  - wsl
  - windows
  - virtualbox
  - guia-rapido
star: false
category: blog
author: keven
description: "Guia rápido de como configurar o WSL para executar em conjunto com o VirtualBox"
hidden: false
---

*Recentemente o VirtualBox fez algumas atualizações, criando assim uma serie de problemas entre essas duas ferramentas (VirtualBox e WSL). Com isso, algumas pessoas foram obrigadas a escolher entre habilitar ou desabilitar o hipervisor, e escolher entre o WSL ou VirtualBox. Até a data dessa postagem, não há uma solução nas versões mais recentes, entretanto, é possível contorná-la.*

## >_ O Problema com o VirtualBox

O problema consiste em usar a versão mais atualizada do VirtualBox, em conjunto com o WSL (Subsistema do Linux no Windows). Quando tentava criar uma máquina com o VirtualBox, a seguinte mensagem aparecia na tela:

{% highlight raw %}
[...] NEMR0InitVMPart2 failed: VERR_NEM_INIT_FAILED (VERR_NEM_VM_CREATE_FAILED)
{% endhighlight %}

Com isso a máquina não é iniciada. A sugestão que é dada na maioria dos fóruns, seria a desabilitar o hipervisor, que está habilitado, usando o seguinte comando, em um powershell:

{% highlight powershell %}
bcdedit /set hypervisorlaunchtype off
{% endhighlight %}

Isso resolve o problema com o Virtualbox, mas criamos outro problema, agora com o WSL.

## >_ O Problema com o WSL

Após fazer o passo anterior, você deve reiniciar o computador. E quando ele inicia, você têm uma nova surpresa: seu WSL não funciona mais. E como faz para fazer funcionar novamente? Tem que habilitar o hipervisor. E nessa brincadeira, você fica tendo que escolher o que é mais importante, o WSL ou o Virtualbox, para seu ambiente de desenvolvimento.

Alguns outros fóruns irão recomendar em ativar recursos adicionais do Windows, que se você não sabe para que servem, sugiro não ativar, pois poderá causar outros problemas. Seria igual a se mexer tentando sair de uma areia movediça, você só se afunda mais. Em meus testes inclusive, nenhum deles funcionou com ambos ativos (WSL + Virtualbox).

Se fez o passo anterior e quer de volta o seu WSL, use o comando abaixo, e reinicie sua máquina:

{% highlight powershell %}
bcdedit /set hypervisorlaunchtype auto
{% endhighlight %}

## >_ A Solução: Contornando os Problemas com Downgrade

A solução que encontrei para contornar este problema é fazer o downgrade do Virtualbox para uma versão anterior que não estivesse com este bug. Com isso, fiz o downgrade para a versão 6.1.26 (atualmente estamos na 6.1.28), e reiniciei a máquina host. Após isso, ambos os dois, WSL e Virtualbox, voltaram a funcionar. Você pode instalar a mesma versão que instalei através deste [link][1].

Ainda assim, é possível que encontre alguns problemas. Um deles pode ser derivado das suas antigas máquinas virtuais. Algumas delas podem não ser mais acessíveis. Se acontecer algum erro como esse, sugiro remover manualmente o diretório da máquina, que fica localizado por padrão em <span class="evidence">C:\\\<nome_do_usuario>\VirtualBox VMs\\</span>

<div class="breaker"></div>

[1]: https://www.virtualbox.org/wiki/Download_Old_Builds_6_1 