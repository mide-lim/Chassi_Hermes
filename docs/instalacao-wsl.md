# Instalação manual da base WSL do Chassi_Hermes

[Voltar ao início](../README.md)

## Objetivo e estado

Este guia prepara uma distribuição Ubuntu 24.04 em WSL 2, com armazenamento em outro disco interno e máximo configurado em 512 GB.

A receita de referência foi aplicada manualmente com **WSL 2.6.3.0**. A base Linux resultante foi confirmada por saídas de terminal compartilhadas. Este documento usa o nome genérico **Agentic-Local** e o caminho de exemplo **D:\WSL\Agentic-Local**. Esses valores devem ser adaptados antes de uma reprodução.

A instalação do Hermes, a conexão do MCP local e a execução dos agentes pertencem a etapas posteriores. A reprodução integral deste guia por terceiro e a recuperação completa do ambiente ainda não foram validadas.

## Antes de começar

- O WSL precisa estar instalado e apto a executar distribuições em modo WSL 2.
- O disco escolhido precisa estar disponível para o Windows, com espaço livre para os dados que serão produzidos.
- O nome da nova distribuição e a pasta de destino precisam estar disponíveis. Se já houver uma instalação com esses valores, confira sua finalidade antes de continuar.
- O exemplo usa uma pasta no disco existente. Não é necessário criar outra partição para seguir este modelo.

A versão do pacote WSL, como 2.6.3.0, é diferente do modo de uma distribuição, identificado como WSL 1 ou WSL 2. A [documentação de comandos](https://learn.microsoft.com/en-us/windows/wsl/basic-commands) explica as duas consultas. A [release 2.6.3](https://github.com/microsoft/WSL/releases/tag/2.6.3) identifica a versão usada como referência.

## Onde executar cada comando

**PowerShell, no Windows:** use os blocos marcados como `powershell`. O terminal normalmente apresenta um prompt iniciado por `PS`.

**Shell do Ubuntu, no Linux:** use os blocos marcados como `bash`. Eles são executados depois de abrir a distribuição.

Os blocos abaixo contêm somente os comandos; não acrescente o texto do prompt ao copiá-los.

## 1. Conferir a base existente

No PowerShell:

~~~powershell
wsl --version
wsl --list --verbose
wsl --list --online
~~~

Verifique a versão do pacote, as distribuições já registradas e a disponibilidade de Ubuntu-24.04. A consulta de distribuições instaladas também permite conferir a coluna VERSION, cujo valor esperado para a nova instalação é 2.

Caso precise consultar as opções aceitas pelo executável instalado, veja a ajuda diretamente:

~~~powershell
wsl --help
~~~

A ausência de resultado em um filtro de texto não é suficiente para concluir que uma opção não existe. A trilha deste guia utiliza a configuração `defaultVhdSize` antes da criação, sem depender do argumento `--vhd-size`.

Fonte: [Basic commands for WSL](https://learn.microsoft.com/en-us/windows/wsl/basic-commands).

## 2. Definir o máximo do novo disco

A configuração fica no perfil do usuário do Windows, em **%UserProfile%\.wslconfig**, fora da distribuição Linux. Para abrir esse arquivo no PowerShell:

~~~powershell
notepad "$env:USERPROFILE\.wslconfig"
~~~

Se o arquivo já existir, preserve suas opções. Na seção `[wsl2]`, acrescente ou atualize a chave abaixo. Se a seção já existir, use a mesma seção, sem duplicá-la.

~~~ini
[wsl2]
defaultVhdSize=512GB
~~~

O [arquivo de exemplo](../examples/wslconfig.example) contém essa configuração com comentários. Ele é uma referência para edição, não um arquivo para substituir automaticamente toda a configuração existente.

`.wslconfig` tem alcance global para WSL 2. `defaultVhdSize` estabelece o tamanho padrão usado na criação de novos discos; alterar essa chave não é um procedimento para reduzir um disco existente. Valores de tamanho sem unidade são interpretados em bytes, por isso use a unidade explicitamente.

Fonte: [Advanced settings configuration in WSL](https://learn.microsoft.com/en-us/windows/wsl/wsl-config).

### O que significam os 512 GB

Esse valor é o máximo lógico do disco virtual. No funcionamento dinâmico padrão, o arquivo cresce conforme os dados são gravados; o máximo não representa uma reserva imediata de todo esse espaço no disco do Windows.

O espaço livre da unidade continua sendo compartilhado com os demais arquivos. A capacidade máxima vista no Linux e o espaço fisicamente ocupado pelo arquivo do disco virtual são medidas diferentes. A recuperação de espaço físico após apagar arquivos também precisa ser tratada separadamente do tamanho máximo.

Para uma instalação limpa, definir o máximo antes da criação evita começar com outro tamanho e depois precisar reduzi-lo. A Microsoft documenta a expansão e observa que a redução é mais complexa.

Fonte: [How to manage WSL disk space](https://learn.microsoft.com/en-us/windows/wsl/disk-space).

## 3. Carregar a configuração e criar a distribuição

Salve o arquivo. Finalize trabalhos que estejam abertos no WSL antes do próximo comando: `wsl --shutdown` encerra as distribuições em execução.

No PowerShell, execute os comandos abaixo em sequência. Substitua o disco e a pasta se seu destino for diferente:

~~~powershell
wsl --shutdown
wsl --set-default-version 2
New-Item -ItemType Directory -Path "D:\WSL" -Force | Out-Null
wsl --install -d Ubuntu-24.04 --name Agentic-Local --location "D:\WSL\Agentic-Local"
~~~

`--set-default-version 2` seleciona o modo das novas distribuições. `--name` define o nome registrado e `--location` indica a pasta de armazenamento. A instalação pode abrir o Ubuntu e solicitar a criação de um usuário Linux.

Use uma conta Linux própria para o trabalho cotidiano e acompanhe os prompts do instalador. Se algum comando apresentar erro, guarde a mensagem e confira o estado antes de repetir a instalação.

Fontes: [comandos de instalação e configuração do WSL](https://learn.microsoft.com/en-us/windows/wsl/basic-commands), [arquitetura moderna das distribuições e suas opções de instalação](https://devblogs.microsoft.com/commandline/whats-new-in-the-windows-subsystem-for-linux-in-november-2024/) e [configuração da primeira inicialização](https://learn.microsoft.com/en-us/windows/wsl/build-custom-distro).

## 4. Verificar o que foi criado

### No PowerShell

~~~powershell
wsl --list --verbose
~~~

Confirme a presença de `Agentic-Local` e VERSION igual a `2`. O estado pode ser Running ou Stopped, dependendo de a distribuição estar aberta.

Para consultar a pasta registrada, sem editar o Registro do Windows:

~~~powershell
Get-ChildItem -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Lxss' |
    Where-Object { $_.GetValue('DistributionName') -eq 'Agentic-Local' } |
    ForEach-Object { $_.GetValue('BasePath') }
~~~

O resultado deve corresponder à pasta de destino escolhida. Esse registro é a evidência da localização efetiva; o texto do comando de instalação expressa apenas a localização solicitada.

Fonte da consulta de localização: [How to locate the .vhdx file and disk path](https://learn.microsoft.com/en-us/windows/wsl/disk-space).

Abra a distribuição:

~~~powershell
wsl -d Agentic-Local
~~~

### No shell do Ubuntu

~~~bash
cat /etc/os-release
whoami
df -h /
~~~

Confirme Ubuntu 24.04, o usuário criado e uma capacidade coerente com o limite configurado. Os números de uso e a apresentação da capacidade podem variar; não use uma saída numérica copiada como critério rígido para outra instalação.

`df -h /` informa o sistema de arquivos Linux. Ele não substitui a verificação de espaço livre no disco do Windows.

Fonte: [verificação de espaço no WSL](https://learn.microsoft.com/en-us/windows/wsl/disk-space#how-to-check-your-available-disk-space).

## 5. Registrar a evidência

No registro privado da instalação, anote:

- Data e versão do pacote WSL.
- Distribuição, versão Linux e modo WSL.
- Nome registrado, BasePath e usuário Linux.
- Limite configurado e saída de `df -h /`.
- Erros, ajustes e diferenças em relação a esta receita.

Ao produzir a versão pública, substitua identificadores particulares por exemplos. O resultado desta etapa é uma base Linux verificada. A validação do Hermes, do MCP e da recuperação do trabalho será registrada quando essas etapas forem executadas.

## Dúvidas comuns

| Situação | Próximo passo |
| --- | --- |
| O comando foi colado no terminal errado | Volte à seção sobre PowerShell e shell Linux |
| O nome da distribuição já existe | Identifique a instalação existente antes de escolher outro nome ou outro procedimento |
| A pasta registrada difere do destino pretendido | Registre BasePath e a saída da instalação; resolva a diferença antes de incorporar o ambiente à arquitetura |
| O disco virtual mostra capacidade diferente do espaço livre do Windows | Confira separadamente limite virtual, uso Linux e espaço físico disponível |
| A instalação falhou | Preserve a mensagem e verifique se houve criação parcial antes de repetir comandos |

## Próxima etapa

Siga para [Persistência e evolução](persistencia-e-evolucao.md). A integração agêntica deverá ser consolidada na referência remota e depois reproduzida neste ambiente com versões e diferenças documentadas.
