# Persistência e evolução do Chassi_Hermes

[Voltar ao início](../README.md)

## Objetivo

Persistir a infraestrutura significa conservar informação suficiente para reconstruir o software, recuperar o estado necessário e compreender as decisões tomadas. Cada uma dessas necessidades tem um material de origem e uma forma de verificação.

Esta entrega registra uma base WSL aplicada manualmente e uma arquitetura agêntica proposta. Os mecanismos de backup do runtime e a reconstrução completa continuam pendentes de implementação e ensaio.

## O que vai para cada lugar

| Material | Destino previsto | Evidência de que está preservado |
| --- | --- | --- |
| Código, documentação e configuração compartilhável | Repositório responsável no GitHub | Commit enviado ao remoto e identificado no registro da instalação |
| Configuração particular do Hermes | Repositório privado do Hermes | Versão registrada, com credenciais referenciadas sem seus valores |
| Contexto e memória textual selecionada de um projeto | Repositório privado daquele projeto | Decisões e checkpoints enviados ao remoto |
| Bancos, filas, arquivos de trabalho e demais estados mutáveis | Backup consistente, separado do histórico de código | Cópia recuperável e restauração verificada |
| Credenciais | Mecanismo de guarda e provisionamento definido para a instalação | Forma documentada de repor o acesso, sem publicar os valores |
| Modelos de IA e arquivos grandes | Origem e versão registradas; armazenamento conforme a necessidade | Possibilidade de obter novamente ou restaurar o conteúdo necessário |

Um commit existente apenas na máquina local ainda não está persistido no remoto. O repositório também não preserva automaticamente um processo em execução, uma chamada externa em andamento ou dados que nunca foram registrados.

## Fronteira entre chassi e almas

O repositório público [Chassi_Hermes](https://github.com/mide-lim/Chassi_Hermes) guarda procedimentos genéricos, interfaces e exemplos. O repositório privado do Hermes guarda a configuração da instância e as informações de coordenação que abrangem seus projetos.

Cada projeto mantém autoridade sobre seu conteúdo detalhado. O Hermes pode conservar um índice e resumos com referências para localizar esse conteúdo, reduzindo a necessidade de manter cópias independentes da mesma informação.

Os destinos e as referências dos repositórios privados ficam na documentação particular. Os exemplos deste repositório usam identificadores genéricos.

Uma publicação derivada de conteúdo privado passa por seleção e revisão. A base pública deve continuar utilizável com exemplos genéricos e sem depender de acesso aos repositórios particulares.

## Mesma arquitetura em dois ambientes

Local e VPS devem compartilhar uma base lógica identificável: componentes, contratos, versões relevantes e procedimentos. Dados, credenciais, capacidade e recursos de execução podem ser específicos de cada ambiente.

A VPS será a primeira referência completa da arquitetura. A base WSL local já preparada será usada para reproduzir essa referência, desenvolver mudanças e fazer ensaios de reconstrução.

Para a operação remota continuar com o computador local desligado, cada tarefa precisa ter disponíveis no ambiente responsável seus insumos, configuração e estado de execução necessários. Uma referência a um arquivo existente somente no computador não cumpre essa condição.

Um registro inicial de compatibilidade pode ser pequeno: referência do chassi, versão do Hermes, revisões dos projetos envolvidos e formato dos checkpoints. O objetivo é identificar a combinação usada, evitando depender de uma descrição vaga como “a última versão”.

## Registro e transferência de tarefas

O [modelo de tarefa](../templates/tarefa.json) é um ponto de partida. Ele contém origem, contexto, executor, checkpoint, incerteza, referência ao resultado e próximo passo.

| Campo | Uso |
| --- | --- |
| `objetivo` | Definir o resultado esperado da tarefa |
| `atualizado_em` | Registrar a data e hora da atualização em ISO 8601, quando conhecidas |
| `origem` | Localizar o pedido que deu origem ao trabalho |
| `contexto` | Identificar o projeto, sua revisão e as referências necessárias |
| `executor` | Identificar o ambiente e o responsável pela execução |
| `checkpoint` | Registrar o último passo comprovadamente concluído |
| `incerteza` | Descrever algo cujo resultado ainda precisa ser conferido |
| `referencia_resultado` | Localizar a saída produzida |
| `proximo_passo` | Registrar a ação de continuidade pretendida |

O exemplo começa como `planejada`, com campos ainda não definidos. Copiar o arquivo, preencher um executor ou escrever um próximo passo não concede autorização para executar ações.

Estados sugeridos para a evolução do registro: `planejada`, `em_execucao`, `interrompida`, `resultado_incerto` e `concluida`. Nenhum mecanismo automático de transição está implementado neste repositório.

### Passagem explícita por tarefa

1. Consultar o estado da tarefa no ambiente responsável.
2. Registrar o último checkpoint e as referências aos resultados disponíveis.
3. Resolver ou sinalizar efeitos externos cujo resultado permaneça incerto.
4. Encerrar ou desabilitar a execução anterior antes de transferir a responsabilidade daquela tarefa.
5. Disponibilizar os insumos necessários e retomar a partir do passo seguro no outro ambiente.

Um registro no Git documenta essa decisão, mas não bloqueia sozinho uma execução antiga. A mudança precisa corresponder ao estado real do executor. Se o ambiente anterior estiver inacessível, sua situação deve permanecer marcada como incerta até haver evidência suficiente para decidir a retomada.

O fim de uma conversa ou a troca de MCP também não confirma que um processo terminou. Antes de repetir uma ação externa, confira se ela já produziu resultado.

## Reconstrução e recuperação

Há três verificações diferentes a planejar:

| Verificação | Pergunta que responde | Estado nesta entrega |
| --- | --- | --- |
| Reconstrução do software | Uma instalação limpa pode executar a base documentada? | Base WSL aplicada manualmente; reconstrução completa pendente |
| Recuperação do estado | Uma cópia permite recuperar memória e trabalho necessários? | Pendente |
| Continuidade de tarefa | O trabalho pode prosseguir em outro ambiente sem repetir uma etapa concluída? | Pendente |

Quando os componentes forem instalados, identifique seus caminhos reais de estado e como produzir uma cópia consistente. Inclua também a configuração do host necessária à reconstrução, como `.wslconfig`, que fica fora da distribuição Linux.

O backup precisa cobrir a falha pretendida: uma cópia guardada apenas no mesmo disco depende da sobrevivência desse disco. O destino, a frequência e o alcance serão definidos conforme o estado que passar a existir.

Os arquivos VHD, bancos ativos, cópias completas e credenciais não pertencem ao histórico deste chassi público. O [.gitignore](../.gitignore) fornece exclusões iniciais; ele não remove conteúdo que já tenha sido versionado.

Referências técnicas para a base WSL: [localização e alcance de .wslconfig](https://learn.microsoft.com/en-us/windows/wsl/wsl-config) e [comandos de exportação e importação](https://learn.microsoft.com/en-us/windows/wsl/basic-commands).

## Laboratório de IA local

Os experimentos de IA local também terão repositórios, com um nível de registro leve:

- Objetivo e comando ou procedimento para repetir o experimento.
- Dependências relevantes e identificação do modelo utilizado.
- Resultado selecionado, limitação encontrada ou aprendizado.

Não se exige recuperação rigorosa de cada tentativa, CI completo ou publicação para todo experimento. A exigência cresce quando um resultado passa a integrar a infraestrutura usada regularmente. Credenciais e dados particulares continuam sob seus destinos próprios.

## Evidência e linguagem de estado

Use os termos de modo consistente:

| Estado | Significado |
| --- | --- |
| Planejado | Procedimento ou comportamento que se pretende obter |
| Em execução | Trabalho iniciado, ainda sem resultado final confirmado |
| Validado | Critério observado, acompanhado da evidência correspondente |
| Pendente ou bloqueado | Informação, capacidade ou dependência ainda necessária |

Um tutorial escrito é documentação disponível. Uma execução bem-sucedida é evidência daquele ambiente e daquela versão. A reprodução por outra pessoa e a restauração de backup acrescentam verificações próprias.

Para relatar a jornada publicamente, registre problema, decisão, ação realizada, resultado observado e próximo passo. Mantenha as dificuldades e mudanças de direção que ajudem o leitor a reproduzir o aprendizado.

## Próximos incrementos

1. Introduzir CI para as verificações úteis da documentação e dos modelos existentes.
2. Consolidar a referência agêntica na VPS, registrando versões, integrações e estado real.
3. Reproduzir essa base no ambiente local e validar uma tarefa representativa.
4. Ensaiar reconstrução e recuperação com dados de teste e atualizar os procedimentos.

Implantação e publicação terão etapas próprias. A escolha da licença do chassi permanece pendente antes da distribuição pública definitiva.
