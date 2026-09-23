# Engenharia de prompts, testes e “cicatrizes”

Este documento registra como as perguntas foram refinadas para produzir respostas mais úteis, verificáveis e adequadas ao estudo de conversão SAP S/4HANA.

## 1. Protocolo do experimento

### Corpus

O caderno foi limitado às cinco fontes oficiais identificadas como F1–F5 no README. Não foram incluídos blogs pessoais, fóruns, conteúdo de clientes ou documentos internos.

### Critérios de qualidade

Cada resposta foi avaliada em cinco dimensões:

| Critério | Pergunta de controle |
| --- | --- |
| Aderência | A resposta atende exatamente ao recorte pedido? |
| Evidência | Afirmações importantes estão associadas a uma fonte? |
| Precisão | Ferramentas e fases não foram confundidas? |
| Utilidade | O resultado pode orientar estudo ou planejamento? |
| Limites | A resposta declara o que não pode concluir? |

### Regra de validação

Uma citação foi considerada útil somente quando a fonte sustentava a afirmação completa. Encontrar a mesma palavra na documentação não foi suficiente.

## 2. Teste 1 — Prompt amplo demais

### Prompt

```text
Resuma tudo o que preciso saber sobre uma migração brownfield para SAP S/4HANA.
```

### Resposta consolidada

> Uma migração brownfield converte o sistema existente para SAP S/4HANA, preservando dados e configurações. O projeto inclui preparação, análise de compatibilidade, conversão técnica, testes e entrada em produção. Ferramentas como Readiness Check, Maintenance Planner, SUM e ATC ajudam nas diferentes etapas.

### O que funcionou

- identificou corretamente o propósito geral do brownfield;
- mencionou ferramentas centrais;
- produziu uma introdução curta.

### O que não funcionou

- não separou atividades obrigatórias de recomendações;
- não explicou quando cada ferramenta é usada;
- não indicou fontes específicas;
- tratou “migração” como um bloco único;
- não registrou decisões que dependem do ambiente real.

### Cicatriz registrada

**Pedir “tudo” reduz a profundidade.** A IA escolhe o que resumir e tende a produzir uma resposta correta, porém genérica. O prompt seguinte passou a exigir estrutura, evidência e limites.

## 3. Teste 2 — Escopo, formato e fontes explícitas

### Prompt

```text
Use somente as fontes F1 a F5. Explique a conversão SAP ECC para SAP S/4HANA em quatro blocos:

1. planejamento e análise;
2. preparação obrigatória;
3. execução técnica;
4. atividades posteriores.

Para cada bloco, informe objetivo, ferramenta, entrada, saída e risco principal. Marque cada afirmação com [F1], [F2], [F3], [F4] ou [F5]. Quando a documentação não permitir uma conclusão, escreva “não determinado pelas fontes”.
```

### Resposta consolidada

| Bloco | Síntese baseada nas fontes | Referência |
| --- | --- | --- |
| Planejamento e análise | Executar o SAP Readiness Check antecipadamente ajuda a identificar impactos e atividades relevantes para o sistema. O escopo real ainda depende da configuração, dados e uso do ambiente produtivo. | [F1][F3] |
| Preparação obrigatória | Maintenance Planner e SI-Check fazem parte da preparação. O primeiro verifica componentes e cria o stack; o segundo verifica simplificações e condições necessárias à conversão. | [F1][F5] |
| Execução técnica | O SUM executa a conversão. Se o banco de origem não for SAP HANA, o DMO pode reunir atualização e migração de banco em um procedimento. | [F5] |
| Atividades posteriores | Código e modificações podem exigir SPDD, SPAU, SPAU_ENH e nova execução dos checks ATC, além de follow-up funcional e técnico. | [F4][F5] |

### Avaliação

- **Aderência:** alta;
- **Evidência:** boa, mas exigiu abertura manual das referências;
- **Precisão:** melhor que o primeiro teste;
- **Utilidade:** suficiente para formar um mapa inicial;
- **Limites:** ainda não detalhou critérios de go/no-go ou regras de reconciliação, porque isso depende do projeto.

### Cicatriz registrada

**Citação não é selo automático de verdade.** Em uma resposta, a referência pode apoiar apenas parte da frase. A revisão precisa abrir o trecho citado e verificar contexto, release e cenário.

## 4. Teste 3 — Distinguir ferramentas parecidas

### Prompt

```text
Compare SAP Readiness Check, Simplification Item Catalog, SI-Check, Maintenance Planner e ATC.

Crie uma tabela com: pergunta respondida pela ferramenta, momento de uso, entrada, saída, caráter obrigatório ou recomendado e erro comum de interpretação.

Não trate Readiness Check e SI-Check como equivalentes. Use apenas F1, F3 e F4 e cite cada linha.
```

### Resposta consolidada

| Ferramenta | Pergunta respondida | Momento | Natureza | Erro comum |
| --- | --- | --- | --- | --- |
| SAP Readiness Check | Quais impactos e atividades merecem atenção no sistema analisado? | Planejamento antecipado | Altamente recomendado | Considerar o relatório uma aprovação técnica final [F1][F3] |
| Simplification Item Catalog | Quais simplificações existem para produtos e versões? | Pesquisa e análise | Fonte de consulta | Assumir que todos os itens do catálogo se aplicam ao sistema [F1] |
| SI-Check | Quais simplificações e condições bloqueiam ou impactam a conversão? | Preparação e novamente durante o SUM | Obrigatório | Executar somente próximo ao cutover [F1] |
| Maintenance Planner | Componentes, add-ons e business functions são compatíveis e qual stack será usado? | Preparação | Obrigatório para o fluxo descrito | Ignorar dependências de add-ons [F1] |
| ATC | Que objetos customizados apresentam findings relacionados ao SAP S/4HANA? | Antes e depois da conversão, conforme estratégia | Altamente recomendado | Transformar número de findings em estimativa sem analisar uso e complexidade [F4] |

### Avaliação

Esse formato reduziu a confusão entre ferramentas e gerou material diretamente reutilizável no miniguia. A restrição de fontes ajudou a impedir associações não sustentadas.

### Cicatriz registrada

**Ferramentas semelhantes precisam de perguntas contrastantes.** Pedir apenas “explique cada ferramenta” gera definições isoladas; pedir diferenças de entrada, saída e momento revela o papel real de cada uma.

## 5. Teste 4 — Código customizado com priorização

### Prompt

```text
Com base apenas em F4, proponha uma sequência para reduzir o esforço de adaptação de código customizado.

Separe:
- scoping por uso;
- análise técnica;
- adaptação;
- validação.

Não invente percentual de redução, prazo ou produtividade. Indique transações e ferramentas somente quando aparecerem na fonte.
```

### Resposta consolidada

1. Coletar evidência de uso com SCMON e agregar os dados com SUSG. A documentação recomenda uma janela longa o suficiente para representar a operação e cita pelo menos um ano quando possível. [F4]
2. Usar o scoping para decidir quais objetos realmente precisam seguir para SAP S/4HANA e quais objetos sem uso podem ser retirados de forma controlada. [F4]
3. Executar os checks SAP S/4HANA pelo Custom Code Migration app ou ATC central, conforme arquitetura disponível. [F4]
4. Classificar findings por objeto, impacto e disponibilidade de quick fix. [F4]
5. Durante e após a conversão, realizar as adaptações relacionadas a SPDD, SPAU e SPAU_ENH e repetir checks ATC. [F4]
6. Avaliar consultas críticas com dados de desempenho quando necessário; o guia menciona SQL Monitor para localizar SQLs relevantes. [F4]

### Avaliação

O bloqueio explícito a números inventados foi importante. Sem essa regra, respostas anteriores tendiam a transformar uma recomendação qualitativa em estimativas não sustentadas.

### Cicatriz registrada

**A IA preenche lacunas com números plausíveis quando o pedido sugere uma estimativa.** Em documentação técnica, proibir métricas inventadas é tão importante quanto pedir uma fonte.

## 6. Teste 5 — Procurar lacunas em vez de respostas

### Prompt

```text
Liste dez decisões de uma conversão brownfield que não podem ser tomadas apenas com F1–F5. Para cada decisão, informe o dado real que faltaria coletar e quem deveria participar da decisão. Não preencha as lacunas com suposições.
```

### Resposta consolidada

| Decisão | Informação ausente | Participantes sugeridos |
| --- | --- | --- |
| Release-alvo | PAM, contratos, estratégia e compatibilidade do landscape | Arquitetura, Basis, negócio |
| Janela de downtime | Volumes, ensaios, SLA e calendário comercial | Basis, operações, negócio |
| Add-ons | Inventário, versões e posicionamento do fornecedor | Basis, arquitetura, fornecedor |
| Escopo de código | Uso, criticidade e dependências de objetos Z | ABAP, funcionais, donos de processo |
| Estratégia de dados | Volume, qualidade, retenção legal e histórico necessário | Dados, jurídico, negócio |
| Sequência de empresas | Dependências organizacionais e interfaces | Programa, finanças, supply chain |
| Plano de testes | Processos críticos, riscos e ambientes disponíveis | QA, funcionais, usuários-chave |
| Reconciliação | Indicadores, tolerâncias e fontes oficiais | Dados, controles, donos de processo |
| Go/no-go | Critérios de aceite, defeitos e contingência | Steering committee e líderes de workstream |
| Modelo de suporte | Volume esperado de incidentes e capacidade do time | Operações, suporte, negócio |

### Avaliação

Esse foi o teste mais importante para o pensamento crítico. Em vez de ampliar uma síntese, ele delimitou onde a documentação termina e onde começa a responsabilidade do projeto.

### Cicatriz registrada

**Uma boa resposta também sabe parar.** A utilidade profissional da IA aumenta quando ela identifica a informação que falta, em vez de ocultar a incerteza com uma conclusão genérica.

## 7. Troubleshooting durante o estudo

| Problema observado | Causa provável | Ajuste aplicado |
| --- | --- | --- |
| Resposta genérica | Prompt amplo e sem entregável definido | Dividir por fase e especificar tabela, campos e público |
| Citações pouco úteis | Frases com várias afirmações diferentes | Solicitar uma afirmação por linha e referência correspondente |
| Confusão entre Readiness Check e SI-Check | Ferramentas perguntadas isoladamente | Exigir comparação de objetivo, momento, entrada e saída |
| Mistura de releases | Fontes de anos diferentes ou pergunta sem release | Priorizar documentação 2025 e pedir alerta para variações |
| Números inventados | Pedido de estimativa sem dados reais | Proibir percentuais, prazos e esforço não presentes nas fontes |
| Prescrição excessiva | IA interpreta documentação como plano fechado | Pedir “o que não pode ser decidido com estas fontes” |
| Resposta longa sem hierarquia | Muitos objetivos em um único prompt | Definir leitor, limite e ordem de prioridade |
| Termos em inglês sem contexto | Documentação técnica preserva nomenclatura original | Manter o termo oficial e acrescentar explicação em português |

## 8. Prompt final para gerar o miniguia

```text
Atue como facilitador de aprendizagem em SAP S/4HANA, não como responsável por uma decisão de projeto.

Use somente as fontes F1–F5. Crie um miniguia em português sobre conversão SAP ECC para SAP S/4HANA em abordagem brownfield para um profissional que já conhece processos SAP, mas está estudando a jornada completa.

O material deve conter:
1. resumo executivo;
2. comparação brownfield, greenfield e transição seletiva;
3. etapas de diagnóstico, preparação, conversão, testes, cutover e estabilização;
4. tabela de ferramentas e seus papéis;
5. seção sobre código customizado;
6. controles de dados e reconciliação;
7. riscos e mitigações;
8. checklist de prontidão;
9. glossário;
10. dez prompts para revisão.

Regras:
- cite [F1] a [F5] nas afirmações técnicas;
- diferencie obrigatório, recomendado e dependente do cenário;
- não invente prazo, esforço, percentual ou transação;
- declare quando uma decisão exige dados do sistema real;
- use linguagem objetiva e evite afirmações absolutas;
- finalize com limitações do material.
```

## 9. Como validar uma resposta no NotebookLM

1. abra a citação apresentada ao lado da afirmação;
2. leia o parágrafo anterior e o posterior;
3. confirme produto, release e cenário;
4. verifique se a fonte descreve obrigação, recomendação ou possibilidade;
5. divida frases que misturam fatos diferentes;
6. registre divergências entre fontes;
7. marque como inferência qualquer conclusão que não esteja expressa;
8. remova prazos e números sem base;
9. peça uma segunda resposta com foco em lacunas;
10. só então atualize o material final.

## 10. Síntese da experiência

O maior ganho não veio do primeiro resumo, mas da evolução das perguntas. Ao exigir fontes, formato, contraste e limites, o material deixou de ser uma explicação genérica e passou a funcionar como ferramenta de estudo.

O NotebookLM é especialmente útil para navegar um conjunto fechado de documentos e retornar às evidências. Ainda assim, a qualidade do resultado depende da curadoria, do recorte do prompt e da revisão humana. Em temas técnicos, a IA organiza o conhecimento; a responsabilidade pela decisão permanece com o profissional e com a governança do projeto.

## Referências utilizadas

- [F1 — Conversion Guide for SAP S/4HANA 2025](https://help.sap.com/doc/2b87656c4eee4284a5eb8976c0fe88fc)
- [F2 — Converting and Upgrading SAP S/4HANA Systems](https://learning.sap.com/learning-journeys/converting-and-upgrading-sap-s-4hana-systems)
- [F3 — SAP Readiness Check](https://help.sap.com/docs/SAP_READINESS_CHECK)
- [F4 — Custom Code Migration Guide for SAP S/4HANA 2025](https://help.sap.com/doc/9dcbc5e47ba54a5cbb509afaa49dd5a1/2025.000/en-US/CustomCodeMigration_EndtoEnd.pdf)
- [F5 — Conversion to SAP S/4HANA using SUM 2.0](https://help.sap.com/doc/08e459b0d229498fb74efe4b64d34163/convsum20.latest/en-US/cg_s4hana_sum2_unix.pdf)
