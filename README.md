# RAG COM POSTGRESQL 18, PGVECTOR E OLLAMA (QWEN3:8B)

#### Aluno: [Fábio Mendonça dos Santos](https://github.com/fmansantos)
#### Orientadora: [Leonardo Alfredo Forero Mendonza](https://github.com/leofome8) 

---

Trabalho apresentado ao curso [GenAI e LLM MASTER](https://ica.ele.puc-rio.br/cursos/ia-generativa-large-language-models/) como pré-requisito para conclusão de curso e obtenção de crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

- [Link para o código](README.md).

---

### Resumo

Este trabalho apresenta o desenvolvimento de um pipeline avançado de Geração Aumentada por Recuperação (RAG) implementado localmente. O sistema realiza a ingestão automatizada de arquivos PDF, aplicando uma técnica inovadora de Chunking Semântico para segmentar o conteúdo textual com base na proximidade e similaridade vetorial real de sentenças vizinhas, calculada com o modelo `nomic-embed-text`. Para a persistência, os fragmentos de texto e seus respectivos vetores denso de 768 dimensões são armazenados em um banco de dados relacional PostgreSQL 18 estendido com a biblioteca pgvector, utilizando indexação HNSW de alto desempenho para busca por similaridade de cosseno. A síntese da resposta contextual é gerada localmente pelo modelo de linguagem Qwen3:8B via Ollama, e o usuário interage por meio de uma interface integrada em Gradio que suporta buscas textuais, entrada de voz por microfone (STT) e conversão das respostas em áudio sintetizado via gTTS.

### Abstract

This work presents the development of an advanced local Retrieval-Augmented Generation (RAG) pipeline. The system automates PDF document ingestion using an innovative Semantic Chunking technique to segment textual content based on the vector similarity of neighboring sentences, computed through the `nomic-embed-text` model. For data persistence, textual fragments and their corresponding 768-dimensional dense vectors are stored in a PostgreSQL 18 relational database extended with pgvector, utilizing high-performance HNSW index structures for cosine similarity queries. Contextualized answers are synthesized locally with the Qwen3:8B language model via Ollama. User interaction is enabled through a Gradio interface supporting text searches, voice microphonic input (Speech-to-Text), and audio synthesis of responses using gTTS.

### 1. Introdução

Sistemas de perguntas e respostas corporativos dependem crucialmente da precisão conceitual e da segurança dos dados. Abordagens genéricas baseadas em APIs proprietárias externas enfrentam barreiras de privacidade e custos. Este projeto propõe uma infraestrutura de RAG totalmente local de alta performance, utilizando o banco relacional PostgreSQL 18 com a extensão pgvector e modelos open-source orquestrados pelo Ollama. Com a inclusão do fatiamento semântico de sentenças mediado por similaridade de cosseno, garantimos que as ideias recuperadas permaneçam integradas em seu contexto original, superando as desvantagens dos métodos de janelas de caracteres fixas e mitigando alucinações na geração das respostas.

### 2. Modelagem

O fluxo de engenharia de dados e modelagem de componentes compreende as seguintes fases:

1. **Ingestão e Desduplicação:** Varredura automática do diretório de PDFs locais, gerando hash SHA-256 de cada arquivo binário para prevenir processamento duplicado.
2. **Extração Otimizada de Texto:** Utilização da biblioteca PyMuPDF para decodificação rápida de strings por página.
3. **Divisão de Sentenças:** Tokenização de frases em português apoiada pelo NLTK.
4. **Chunking Semântico Adaptativo:** Agrupamento de sentenças adjacentes baseado no limiar de similaridade (threshold de 0.8) utilizando embeddings de sentença.
5. **Armazenamento no PostgreSQL:** Criação de tabelas com colunas estruturadas para caminhos de arquivo, páginas, hashes, texto limpo e vetores do tipo `VECTOR(768)`.
6. **Indexação Vetorial HNSW:** Implementação de índices HNSW com métrica de similaridade de cosseno para buscas logarítmicas rápidas.
7. **Consulta e Síntese:** Geração de prompt estruturado de sistema para o LLM local Qwen3:8B gerenciar e sintetizar o contexto recuperado.

### 3. Resultados

O sistema foi testado localmente com uma carga de 91 documentos PDF corporativos e de pesquisa acadêmica:
- O processamento identificou automaticamente a existência de registros duplicados salvaguardando a integridade da base.
- A busca por similaridade vetorial demonstrou alta relevância na recuperação de dados semânticos com taxas de similaridade de cosseno consistentemente superiores a 0.79.
- O modelo Qwen3:8B respondeu às perguntas de forma extremamente técnica, clara e estritamente amarrada ao contexto do banco de dados.
- A interface Gradio unificou o fluxo, viabilizando perguntas e respostas com áudio funcional local.

### 4. Conclusões

A modelagem de um sistema de RAG local utilizando tecnologias consolidadas como PostgreSQL 18 e pgvector mostra-se viável, escalável e extremamente segura para uso corporativo. O chunking semântico mitigou ruídos de quebra de página de forma exemplar, entregando fragmentos informativos coesos e bem formatados para o LLM, o que elevou drasticamente a confiabilidade das respostas e evitou desvios factuais.

### 5. Referências

- PostgreSQL 18 Global Development Group. pgvector extension specifications.
- Ollama API documentation and reference models (`qwen3:8b`, `nomic-embed-text`).
- PyMuPDF, NLTK sentence tokenization and Gradio web interface guides.

### 6. Instalação

Instale os pacotes requeridos executando:
```bash
pip install pymupdf psycopg[binary] pgvector ollama gradio gtts numpy pandas nltk SpeechRecognition PyAudio
```

Garanta que o Ollama esteja com os modelos locais atualizados:
```bash
ollama pull qwen3:8b
ollama pull nomic-embed-text
```

### 7. Como executar

1. Inicie e configure sua base de dados PostgreSQL 18 local.
2. Configure o caminho do seu diretório de arquivos no início do script.
3. Execute a ingestão de PDFs para povoar a tabela.
4. Execute o bloco da interface Gradio e abra o link gerado no terminal para fazer as perguntas.

---

Matrícula: 123.456.789

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Inteligência Artificial Generativa & Large Language Models*
