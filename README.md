# Desafio Técnico – Engenharia de Dados & IA

## Contexto

Você foi selecionado para a etapa prática do nosso processo seletivo. Este desafio tem como objetivo avaliar sua capacidade de **desenhar, implementar e explicar uma pipeline completa de dados**, utilizando **ferramentas open source**, boas práticas de **engenharia de dados**, **backend**, **arquitetura**, **Docker** e **documentação técnica**.

O desafio deve ser desenvolvido em **até 7 dias corridos**. Ao final, você deverá apresentar sua solução para a próxima etapa da entrevista.

---

## Objetivo Geral

Construir uma **pipeline de dados end-to-end**, desde a ingestão até o consumo, que seja:

* Reprodutível
* Escalável (conceitualmente)
* Observável
* Totalmente containerizada com Docker

---

## Desafio Proposto

Você deverá criar uma pipeline que:

1. Ingira dados de uma fonte externa (API pública ou dataset aberto)
2. Processe, limpe e transforme esses dados
3. Armazene os dados em um banco apropriado
4. Disponibilize os dados para consumo via API ou camada analítica
5. Seja orquestrada e executável localmente via Docker

### Fonte de Dados (Obrigatória)

A pipeline **deve obrigatoriamente utilizar uma API pública**, sendo **fortemente recomendada** a utilização da **PokéAPI**:

* [https://pokeapi.co/](https://pokeapi.co/)

Caso opte por outra API pública, ela deve ser previamente justificada no README (complexidade, volume, estrutura, etc.).

O uso da PokéAPI é interessante porque:

* Possui múltiplos endpoints relacionados
* Dados semi-estruturados e aninhados
* Possibilita modelagem relacional (Pokémon, tipos, habilidades, stats, etc.)
* Permite transformações, normalização e agregações reais

Exemplos de análises possíveis:

* Pokémon por tipo
* Distribuição de stats
* Comparação de gerações
* Pokémon mais fortes por métrica definida

---

O **tema dos dados é livre**, desde que faça sentido para análise e utilize a API escolhida.

---

## Requisitos Técnicos Obrigatórios

### 1. Ingestão de Dados

* Fonte externa (exemplos):

  * API pública (REST)
  * Arquivo CSV/JSON hospedado publicamente
* Ingestão automatizada (script ou job)
* Deve ser possível reexecutar a ingestão sem quebrar o pipeline

### 2. Processamento e Transformação

* Limpeza de dados (nulls, tipos, normalização, etc.)
* Pelo menos uma transformação relevante (agregação, enriquecimento, joins, etc.)
* Código organizado e legível

Ferramentas sugeridas (não obrigatórias):

* Python (pandas, pyarrow, pydantic, etc.)
* SQL
* Spark (opcional, se fizer sentido)

### 3. Armazenamento

* Banco de dados open source (exemplos):

  * PostgreSQL
  * DuckDB
  * ClickHouse
  * SQLite (aceitável se bem justificado)
* Modelagem mínima das tabelas
* Scripts de criação de schema

### 4. Backend / Consumo dos Dados

* Criar uma API simples para consumo dos dados processados
* Endpoints REST (ex: listar dados, agregações, filtros)
* Pode ser apenas leitura

Ferramentas sugeridas:

* FastAPI 
* SQLAlchemy / psycopg2

### 5. Orquestração

* Pipeline executável de forma organizada
* Pode ser:

  * Script principal
  * Makefile
  * Airflow / Dagster (opcional, diferencial)

### 6. Dockerização

* **Tudo deve rodar via Docker**
* docker-compose obrigatório
* Containers separados para:

  * Banco de dados
  * Pipeline / ETL
  * API
  * **Agente de IA** (ver requisito 7)

### 7. Agente de IA (Obrigatório)

Você deverá implementar um **agente de IA** utilizando o **OpenAI Agents SDK (Python)**:

* Repositório: [https://github.com/openai/openai-agents-python](https://github.com/openai/openai-agents-python)
* Docs: [https://openai.github.io/openai-agents-python/](https://openai.github.io/openai-agents-python/)

#### O que o agente deve fazer

O agente deve ser capaz de responder perguntas sobre Pokémon, **consultando dados** de uma das formas abaixo:

1. **Direto na PokéAPI** (chamadas HTTP em tempo real), **ou**
2. **Direto no “fim” da sua pipeline** (ex.: banco/tabelas finais/endpoint da sua API)

> A preferência é que o agente use **os dados finais da pipeline**, para demonstrar integração com o seu armazenamento/modelagem.

#### Requisitos mínimos do agente

* Implementar pelo menos **2 tools (funções)** que o agente possa chamar, por exemplo:

  * `buscar_pokemon(nome_ou_id)`
  * `listar_por_tipo(tipo)`
  * `top_n_por_stat(stat, n)`
  * `comparar_pokemons(pokemon_a, pokemon_b)`
* As tools devem:

  * Ter **docstrings** claras (descrição vira parte do schema de tool)
  * Tratar erros (ex.: Pokémon inexistente, timeouts, etc.)
  * Retornar respostas estruturadas (JSON/dict) quando fizer sentido
* O agente deve rodar via Docker, com configuração por variáveis de ambiente (ex.: `OPENAI_API_KEY`)
* Deve existir um modo simples de execução:

  * CLI (ex.: `docker compose run agent "pergunta..."`) **ou**
  * Endpoint na sua API (ex.: `POST /ask`)

#### Observabilidade (diferencial)

* Logar tool calls e latência por tool
* (Se implementar tracing do SDK, melhor ainda)

---

## Estrutura Esperada do Projeto (Sugestão)

```
project-root/
├── docker-compose.yml
├── README.md
├── .env.example
├── data/
│   └── raw/
├── etl/
│   ├── extract.py
│   ├── transform.py
│   └── load.py
├── api/
│   ├── main.py
│   └── Dockerfile
├── agent/
│   ├── agent_app.py
│   ├── tools.py
│   ├── Dockerfile
│   └── requirements.txt
├── db/
│   └── init.sql
├── scripts/
│   └── run_pipeline.sh
└── requirements.txt
```

A estrutura pode variar, desde que esteja **bem explicada**.

---

## Observabilidade (Desejável)
- Logs estruturados
- Mensagens claras de erro
- Indicação de início/fim das etapas

---

## Documentação (Muito Importante)

O README do projeto deve conter:
- Visão geral da arquitetura
- Tecnologias utilizadas e justificativa
- Como subir o projeto passo a passo
- Como executar a pipeline
- Como acessar a API (exemplos de requests)
- **Como executar o agente de IA** (CLI e/ou endpoint)
- Exemplos de perguntas e respostas esperadas do agente
- Possíveis melhorias futuras

Diagramas simples são bem-vindos (ex: ASCII, Mermaid, imagens).

---

## Critérios de Avaliação

Serão avaliados:
- Clareza da arquitetura
- Qualidade e organização do código
- Boas práticas de engenharia de dados
- Uso correto de Docker
- Capacidade de explicar decisões técnicas
- Simplicidade bem aplicada (evitar overengineering)

---

## Entrega

- Repositório Git (GitHub, GitLab ou similar)
- README completo
- Código executável localmente

Na próxima etapa, você deverá **apresentar sua solução**, explicando:
- Decisões de arquitetura
- Trade-offs
- Pontos fortes e limitações
- Como você escalaria essa solução em produção

---

## Observação Final

Não buscamos uma solução perfeita. Preferimos clareza e boas decisões a excesso de complexidade.

Boa sorte e divirta-se construindo, dúvidas nos envie. 

