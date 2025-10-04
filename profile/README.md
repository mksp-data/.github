# Bem-vindo à Marketscape!

Somos uma organização dedicada a soluções de dados com foco na área comercial, oferecendo o melhor de BI, inteligência geográfica, automação e inovação.

## O que fazemos

- Serviços de dados e integração  
- Armazenamento seguro de dados  
- Desenvolvimento de pipelines e ETL  
- Ferramentas para automação de processos  

## Nossos repositórios

- [Data-Services](https://github.com/mksp-data/Data-Services): Repositório com os serviços de dados da Azure  
- [Power-BI-Assets](https://github.com/mksp-data/Power-BI-Assets): Repositório com todos os nossos relatórios de Power BI e arquivos relacionados  
- [demo-repository](https://github.com/mksp-data/demo-repository): Repositório de demonstração, crie, altere e teste à vontade  

## Como colaborar

Clone o repositório que deseja trabalhar na sua IDE ou gerenciador de Git favorito, faça as alterações ou adições necessárias ao seu escopo de trabalho, commite suas alterações abrindo um Pull Request e solicite a avaliação de um membro com permissões adequadas no repositório.

## Colaboradores

- **Felipe Rocha**  
  [feliperocha-mksp](https://github.com/feliperocha-mksp)  
  _E-mail: felipe.rocha@mksp.com.br_  
  _Engenheiro de Dados/BI e administrador do workspace_

- **Vitor Mariano**  
  [vitormariano-mksp](https://github.com/vitormariano-mksp)  
  _E-mail: vitor.mariano@mksp.com.br_  
  _Analista de BI_

# Padrões de Arquitetura e Governança de Dados - Marketscape

## 1. Introdução

Aqui visamos estabelecer os padrões, as melhores práticas e a filosofia de arquitetura para todos os projetos de dados e Business Intelligence na Marketscape. O objetivo é garantir que as nossas soluções sejam consistentes, escaláveis, seguras e fáceis de manter.  
A adesão a estas diretrizes é fundamental para a qualidade e o sucesso a longo prazo da nossa plataforma de dados.

## 2. Filosofia de Engenharia de Dados

A nossa abordagem à engenharia de dados baseia-se em princípios fundamentais:

* **Imutabilidade da Origem, Rastreabilidade da Transformação:** A camada de dados brutos (Bronze) é um registro imutável do que foi recebido. Todas as transformações, limpezas e regras de negócio são aplicadas nas camadas seguintes (Silver e Gold), garantindo a total rastreabilidade e a capacidade de reprocessar os dados a partir de uma fonte de verdade intacta.

* **Planejamento Consciente, Execução Estratégica:** Cada etapa do pipeline é precedida por análise de impacto, considerando escalabilidade, manutenabilidade e valor gerado. Evitamos decisões impulsivas e priorizamos soluções sustentáveis.

* **Automação com Transparência:** Processos automatizados devem ser rastreáveis, auditáveis e documentados. A automação não substitui o entendimento — ela amplifica a clareza e a consistência.

* **Separação de Responsabilidades por Camada:** Cada camada (Bronze, Silver, Gold) tem um papel claro e não sobreposto. A Bronze registra, a Silver transforma, a Gold entrega valor analítico.

* **Design para Reprocessamento:** Toda arquitetura deve permitir reprocessamento completo ou parcial com mínima intervenção manual, garantindo confiabilidade mesmo em cenários de falha ou revisão de lógica.

## 3. Arquitetura de Dados: O Padrão Medalhão no Microsoft Fabric

Utilizamos a **Arquitetura Medalhão** para organizar os nossos dados em zonas lógicas de qualidade e utilidade.

* **Bronze (Bruta):**  
    * **Propósito:** Armazenar uma cópia exata e imutável dos dados da fonte. Nenhuma transformação de negócio é aplicada aqui, apenas as conversões de tipo de dados necessárias para validar o esquema.  
    * **Artefato no Fabric:** Tabelas dentro de um **Lakehouse (`lh_`)**.  
    * **Exemplo de Tabela:** `brz_faturamento`  
    * **Localização dos Arquivos Originais:** `Files/bronze/{assunto}/nome_do_arquivo.xlsx`

* **Silver (Prata):**  
    * **Propósito:** Fornecer uma "versão da verdade" limpa, validada, de-duplicada e enriquecida. É aqui que aplicamos regras de qualidade, geramos chaves de hash para unicidade e adicionamos colunas de auditoria.  
    * **Artefato no Fabric:** Tabelas dentro de um **Lakehouse (`lh_`)**.  
    * **Exemplo de Tabela:** `slv_faturamento`

* **Gold (Ouro):**  
    * **Propósito:** Apresentar os dados num formato otimizado para análise e consumo de BI. As tabelas aqui são geralmente agregadas e modeladas em esquemas estrela (dimensões e fatos).  
    * **Artefato no Fabric:** Tabelas dentro de um **Data Warehouse (`wh_`)**.  
    * **Exemplo de Tabela:** `gld_dim_cliente`, `gld_fact_faturamento`

## 4. Padrões de Nomenclatura

Utilizamos um padrão consistente para todos os artefatos e colunas para garantir a clareza e a previsibilidade.

* **Formato Geral:** `snake_case` (todas as letras em minúsculas, palavras separadas por underscore `_`)

* **Prefixos de Artefatos no Fabric:**  
    * Workspace: `ws_` (ex: `ws_datacore`)  
    * Lakehouse: `lh_` (ex: `lh_dentalclean`)  
    * Warehouse: `wh_` (ex: `wh_dentalclean`)  
    * Dataflow Gen2: `df_` (ex: `df_brz_slv_faturamento`)  
    * Data Pipeline: `pl_` (ex: `pl_orquestra_faturamento_dentalclean`)  
    * Relatório Power BI: `pbi_` (ex: `pbi_relatorio_vendas`)

* **Prefixos de Tabelas (dentro do Lakehouse/Warehouse):**  
    * Camada Bronze: `brz_` (ex: `brz_faturamento`)  
    * Camada Silver: `slv_` (ex: `slv_faturamento`)  
    * Camada Gold (Dimensão): `gld_dim_` (ex: `gld_dim_produto`)  
    * Camada Gold (Fato): `gld_fact_` (ex: `gld_fact_vendas`)

* **Colunas:**  
    * Utilizar `snake_case` (ex: `faturamento_sem_st`, `data_carga`)  
    * Evitar caracteres especiais, espaços e acentos

## 5. Governança de Código e CI/CD

* **Controle de Versões:** Todos os arquivos de projeto, incluindo relatórios Power BI (`.pbip`), scripts SQL e documentação, devem ser armazenados em repositórios Git no GitHub.

* **Branches:** O desenvolvimento deve ser feito em branches de funcionalidades (`feature/...`) e integrado na branch principal (`main`) através de Pull Requests.

* **CI/CD:** A branch `main` é a fonte da verdade para produção. As atualizações na `main` devem acionar um fluxo de trabalho do GitHub Actions para publicar automaticamente os relatórios nos workspaces corretos do Power BI, movendo-os do ambiente de desenvolvimento para o de produção.
