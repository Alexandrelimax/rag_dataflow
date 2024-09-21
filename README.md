# Pipeline RAG (Retrieval-Augmented Generation) com Apache Beam e Dataflow

Este projeto implementa um pipeline de **Retrieval-Augmented Generation (RAG)** para processamento e vetorização de documentos utilizando **Apache Beam** no **Google Cloud Dataflow**. O pipeline processa arquivos armazenados no Google Cloud Storage, extrai texto, gera embeddings e armazena os dados processados no BigQuery para futuras pesquisas ou aplicações de machine learning.

## Tabela de Conteúdos
- [Visão Geral](#visão-geral)
- [Arquitetura](#arquitetura)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Funcionalidades](#funcionalidades)
- [Fluxo do Pipeline](#fluxo-do-pipeline)
- [Configuração e Instalação](#configuração-e-instalação)
- [Execução do Pipeline](#execução-do-pipeline)
- [Estrutura da Tabela no BigQuery](#estrutura-da-tabela-no-bigquery)
- [Contribuições](#contribuições)
- [Licença](#licença)

## Visão Geral

O objetivo deste pipeline é criar um sistema escalável de processamento de documentos que:
1. Extrai o conteúdo de vários formatos de arquivo (PDF, DOCX, etc.) armazenados no Google Cloud Storage.
2. Divide o conteúdo em chunks gerenciáveis.
3. Gera embeddings para cada chunk utilizando um modelo como o **Vertex AI Embeddings**.
4. Armazena os embeddings e os metadados no **Google BigQuery** para facilitar a busca por similaridade.

## Arquitetura

![Arquitetura RAG](caminho/para/o-diagrama.png)

1. **Google Cloud Storage**: Armazena os documentos (PDF, DOCX, etc.).
2. **Apache Beam + Dataflow**: Distribui o processamento dos documentos entre vários workers.
3. **Vertex AI Embeddings**: Gera embeddings para os chunks de documentos.
4. **Google BigQuery**: Armazena os embeddings e metadados para consultas futuras.

## Tecnologias Utilizadas

- **Google Cloud Storage**: Para armazenamento dos documentos.
- **Google Cloud Dataflow**: Executa os pipelines do Apache Beam de forma gerenciada e escalável.
- **Apache Beam**: Utilizado para processar os documentos de maneira distribuída.
- **Vertex AI**: Usado para gerar embeddings dos chunks de documentos.
- **BigQuery**: Armazena os embeddings e os metadados, permitindo buscas rápidas.
- **Python**: Linguagem principal utilizada na implementação do pipeline.
  
## Funcionalidades

- Processamento escalável de documentos usando Apache Beam e Dataflow.
- Suporte para múltiplos formatos de documentos (PDF, DOCX).
- Divisão eficiente do texto dos documentos em chunks usando LangChain.
- Geração de embeddings com Vertex AI.
- Armazenamento de metadados e embeddings no BigQuery para futuras consultas.

## Fluxo do Pipeline

1. **Listagem de Arquivos**: O pipeline inicia listando os arquivos no bucket especificado no Google Cloud Storage.
2. **Filtragem de Documentos Processados**: Arquivos já processados (baseados nos registros do BigQuery) são filtrados.
3. **Processamento de Documentos**:
    - Extrai o conteúdo dos documentos (PDF ou DOCX).
    - Divide o conteúdo em chunks usando o `RecursiveCharacterTextSplitter`.
4. **Geração de Embeddings**:
    - Gera embeddings usando o modelo `VertexAIEmbeddings` para cada chunk.
5. **Armazenamento no BigQuery**: Os embeddings e os metadados relevantes (nome do documento, chunk, data de processamento) são armazenados em uma tabela no BigQuery.

## Configuração e Instalação

### Pré-requisitos

- Um projeto no Google Cloud com **BigQuery**, **Cloud Storage**, **Vertex AI** e **Dataflow** habilitados.
- Python 3.7+ e as seguintes bibliotecas:
    ```bash
    pip install apache-beam google-cloud-storage google-cloud-bigquery google-cloud-aiplatform langchain
    ```

- Credenciais de conta de serviço para acessar os serviços do GCP (`GOOGLE_APPLICATION_CREDENTIALS`).

### Configuração do Ambiente

1. **Defina o ID do projeto no Google Cloud**:
    ```bash
    export GOOGLE_CLOUD_PROJECT=seu-id-do-projeto
    ```

2. **Defina as credenciais para o GCP**:
    ```bash
    export GOOGLE_APPLICATION_CREDENTIALS=caminho/para/seu/service_account.json
    ```

### Criação da Tabela no BigQuery

Antes de executar o pipeline, crie uma tabela no BigQuery para armazenar os embeddings e os metadados dos documentos. Use o seguinte esquema:

```sql
CREATE TABLE `seu_projeto.sua_dataset.document_embeddings` (
  document_name STRING,
  chunk STRING,
  embedding STRING,
  file_type STRING,
  file_size INT64,
  content_type STRING,
  updated TIMESTAMP,
  processing_date TIMESTAMP
);
