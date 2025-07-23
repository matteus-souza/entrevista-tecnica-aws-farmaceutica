# RELATÓRIO DE IMPLEMENTAÇÃO DE SERVIÇOS AWS

## Informações do Projeto

| Campo | Valor |
|-------|-------|
|**Data** | 15/07/2025 |
|**Empresa** | Abstergo Industries |
|**Responsável** | Mateus de Souza Paula |

## Introdução

Este relatório apresenta o processo de implementação de ferramentas na empresa **Abstergo Industries**, realizado por Mateus Ribeiro. O objetivo do projeto foi elencar **3 serviços da AWS** com o propósito de:

-  Reduzir custos imediatos de infraestrutura e operação
-  Promover escalabilidade
-  Aumentar performance dos sistemas da organização

## Descrição do Projeto

O projeto foi dividido em **3 etapas estratégicas**, contemplando diferentes áreas de tecnologia e operação:

### Etapa 1: Amazon S3

| Aspecto | Detalhes |
|---------|----------|
| **Nome da ferramenta** | Amazon S3 |
| **Foco da ferramenta** | Armazenamento escalável e seguro de dados |
| **Descrição de caso de uso** | Substituição de servidores locais de arquivos pelo Amazon S3, com uso de políticas de ciclo de vida para migração automática para camadas de menor custo (Glacier), reduzindo despesas com manutenção e energia. |

### Etapa 2: AWS Lambda

| Aspecto | Detalhes |
|---------|----------|
| **Nome da ferramenta** | AWS Lambda |
| **Foco da ferramenta** | Execução de código sem necessidade de servidor |
| **Descrição de caso de uso** | Automatização de tarefas de rotina, como processamento de arquivos enviados ao S3 e envio de relatórios, eliminando a necessidade de manter servidores em execução constante. |

### Etapa 3: Amazon EC2 Spot Instances

| Aspecto | Detalhes |
|---------|----------|
| **Nome da ferramenta** | Amazon EC2 Spot Instances |
| **Foco da ferramenta** | Computação de alto desempenho com custos reduzidos |
| **Descrição de caso de uso** | Execução de cargas de trabalho flexíveis, como testes automatizados e simulações de engenharia, utilizando instâncias spot com **até 90% de economia** comparado às instâncias sob demanda. |

## Conclusão

A implementação das ferramentas AWS na **Abstergo Industries** gerou benefícios imediatos:

### Benefícios Alcançados:
-  **Redução significativa de custos operacionais**
-  **Maior elasticidade dos recursos**
-  **Incremento na automação de processos**

### Recomendações:
-  Continuidade da adoção de soluções em nuvem
-  Avaliação periódica de novos serviços oferecidos pela AWS
-  Monitoramento contínuo para agregar ainda mais valor à empresa

## Anexos

-  [Manual de uso do Amazon S3](docs/manual-s3.pdf)
-  [Documentação dos scripts AWS Lambda](docs/lambda-scripts.md)
-  [Planilha comparativa de custos entre instâncias EC2](docs/comparativo-custos.xlsx)

---

** Autor:** Mateus de Souza Paula  
** Contato:** [email](mailto:matteus1907@hotmail.com)  
** Empresa:** Abstergo Industries
