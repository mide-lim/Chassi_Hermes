# Fronteiras entre chassi e almas

[Voltar ao início](../README.md)

## Escopo e estado

O Chassi_Hermes é um projeto de arquitetura de agentes e persistência de contexto. Seu objetivo é oferecer uma estrutura reproduzível que outras pessoas possam adaptar aos próprios trabalhos. Os projetos particulares usam essa estrutura e mantêm seus conteúdos em repositórios próprios.

Este mapa consolida decisões de organização. A documentação inicial e a base Linux de referência existem; a integração completa entre Hermes, MCP, agentes e recuperação permanece planejada.

## Papéis da arquitetura

| Elemento | Responsabilidade prevista |
| --- | --- |
| ChatGPT | Interface principal de conversa, planejamento, desenvolvimento e acompanhamento |
| MCP de cada ambiente | Ponte para as operações e os recursos disponibilizados naquele ambiente |
| Hermes | Orquestração central: localizar contexto, distribuir trabalho e acompanhar resultados |
| Agentes e subagentes | Executar responsabilidades delimitadas com contexto e ferramentas pertinentes |
| GitHub | Versionar código, configuração sem segredos, documentação e registros selecionados; executar verificações de CI a implementar |

Hermes designa aqui o papel central de orquestração. Sua implementação, versão e modo de execução ainda precisam ser identificados. A entrada operacional por MCP também exige definir identidade e privilégios; o planejamento não estabelece que toda operação deva executar como administrador.

## Organização dos repositórios

A divisão compreende um chassi público, uma alma privada do Hermes e uma alma por projeto. A alma do Hermes guarda decisões transversais, configuração particular e um índice dos projetos. Cada projeto guarda seu código, requisitos, contexto, memória e resultados versionáveis.

O índice pode conter referências e resumos suficientes para localizar o trabalho. Os detalhes permanecem no repositório responsável, evitando manter várias versões independentes da mesma informação.

## Matriz de responsabilidade

| Tema | Mecanismo reutilizável no chassi | Conteúdo particular nas almas |
| --- | --- | --- |
| Instalação | Procedimentos, dependências e modelos de configuração | Caminhos reais, versões adotadas e inventário da instalação |
| Orquestração | Contratos de delegação, retorno e conclusão | Prioridades, projetos ativos e regras particulares de coordenação |
| Agentes | Papéis genéricos e interfaces de ferramentas | Especializações, instruções e critérios próprios de cada projeto |
| Skills | Procedimentos genéricos e formas de integração | Skills específicas, escolhas de uso e adaptações particulares |
| MCP | Adaptadores, contratos e exemplos de configuração | Endereços reais, serviços habilitados e referências aos acessos |
| Permissões | Mecanismos e modelos de restrição | Identidades, escopos e regras concretas da instalação |
| Memória | Formatos, procedimentos de registro e recuperação | Preferências, decisões, contexto e aprendizados selecionados |
| Tarefas | Esquema de estados, checkpoints e resultados | Tarefas reais, responsável, execução observada e próxima ação |
| Aplicações | Exemplos genéricos usados para explicar a arquitetura | Código, dados, requisitos e entregas dos projetos |
| CI | Verificações genéricas e modelos reutilizáveis | Testes e configurações específicos de cada projeto |
| Recuperação | Procedimentos e critérios de validação | Inventário do estado necessário e referências aos backups |
| Medições | Formato de registro e método de avaliação | Tempos, erros, volumes, custos e resultados da operação particular |

Credenciais não entram nos repositórios, inclusive privados. As almas guardam referências e instruções para recuperar o acesso, sem os valores secretos.

A classificação depende do conteúdo. Uma skill particular pode originar uma versão genérica depois de revisão. Publicar um projeto privado exige preparar uma edição independente com o conteúdo e o histórico aprovados para divulgação.

## Memória, execução e armazenamento

O plano distingue três registros: histórico relevante da comunicação, memória atual e estado de execução. Uma decisão vigente não deve se perder entre mensagens antigas; um resultado ainda incerto não deve ser apresentado como concluído.

O [modelo de tarefa](../templates/tarefa.json) inicia essa organização: objetivo, contexto, ambiente executor, último passo confirmado, incerteza, resultado e próxima ação. O formato pertence ao chassi; os registros preenchidos pertencem à alma correspondente.

A alma representa a configuração e o contexto particular, mas seus dados não precisam estar todos dentro do Git. Bancos, filas, volumes e arquivos grandes exigirão armazenamento e recuperação adequados. O repositório deve identificar esses componentes e explicar como obter uma cópia consistente e restaurá-la.

O fluxo previsto encerra uma etapa relevante com atualização da documentação, revisão do conteúdo, commit e envio ao remoto. A conversa precisa gerar registros explícitos para alimentar esse fluxo. A sessão de um modelo e os processos em execução também requerem mecanismos próprios quando sua recuperação for necessária.

Os detalhes estão em [Persistência e evolução](persistencia-e-evolucao.md).

## Dois ambientes persistentes

Local e VPS deverão compartilhar arquitetura lógica, contratos e versões compatíveis. Hardware, credenciais, dados disponíveis e configuração podem variar.

O local será o ambiente principal de desenvolvimento, testes e reconstrução. A VPS terá hospedagem pública e recursos para continuar o trabalho quando o local estiver indisponível. Essa continuidade requer insumos e acessos disponíveis na VPS, preservando os serviços que já estejam hospedados.

A referência completa será consolidada primeiro na VPS e depois reproduzida localmente. A base WSL preparada é um ponto de partida para essa reprodução.

Inicialmente, a transferência será deliberada por tarefa. Registrar o responsável documenta a decisão; o procedimento também deve conferir processos anteriores e efeitos ainda incertos. Trocar de MCP não comprova que uma execução anterior terminou.

Vercel e Sites poderão apoiar previews e showcases. As validações de permissões, integrações e persistência precisarão de ambientes que reproduzam as condições avaliadas.

## Laboratório de IA local

Os experimentos com IA local terão repositórios para código, configuração, anotações e resultados selecionados. Seu propósito inicial é aprendizado.

Esse laboratório terá menor exigência de recuperação. Preservar integralmente cada sessão, cada tentativa e os estados internos do modelo não é requisito inicial; disponibilizar a mesma capacidade na VPS também não é. A arquitetura principal e os projetos que dependem de continuidade seguem os procedimentos de persistência definidos para eles.

## Decisões e escolhas restantes

| Estado | Conteúdo |
| --- | --- |
| Definido no planejamento | Separação entre chassi e almas; dois ambientes; ChatGPT como interface; MCP por ambiente; Hermes como orquestrador; transferência explícita; laboratório local com rigor menor |
| Proposta para implementar | Rotinas de memória, contratos executáveis, instalação reproduzível, CI, procedimentos de recuperação e medições |
| A escolher ou confirmar | Software do Hermes, versões, serviços necessários, armazenamento, autenticação, privilégios, dependências externas e modo de execução dos agentes |
| A demonstrar | Reconstrução completa, restauração dos dados e continuidade de uma tarefa em outro ambiente |

## Aprendizado e avaliação do desenho

A trilha pública começa com versionamento e registro de contexto no GitHub, avança para CI, ambiente Linux, agentes e integração via MCP, e chega à operação remota. A VPS aparece quando acrescentar uma capacidade necessária. Nenhum workflow de CI está implementado nesta etapa.

Um desenho futuro deverá explicitar o caminho das tarefas, dos dados e dos registros de memória. Para avaliar desempenho, precisaremos relacioná-lo à carga esperada, concorrência, recursos disponíveis, dependências e tempos observados. Compatibilidade entre ambientes e capacidade de reconstrução são critérios distintos de velocidade.

As metas de desempenho serão definidas a partir das tarefas reais e verificadas por medições. O mapa estabelece responsabilidades e fronteiras; a implementação deverá produzir as evidências correspondentes.

