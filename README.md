# Sistema RAG (Retrieval-Augmented Generation) com Ollama & LangChain

Sistema 100% local de perguntas e respostas baseado em documentos PDF usando embeddings densos (`mxbai-embed-large`), banco vetorial (`ChromaDB`) e engenharia de prompt com *grounding* estrito no `llama3.2`.

## Visão Geral

Este projeto implementa um pipeline completo de RAG (Retrieval-Augmented Generation) que permite consultar bases de conhecimento em PDF sem depender de APIs externas pagas:

1. Carrega e processa PDFs da pasta `base/`
2. Divide os documentos em chunks preservando contexto semântico (`chunk_size=2000`)
3. Gera embeddings vetoriais de 1024 dimensões usando o modelo `mxbai-embed-large` do Ollama
4. Armazena e indexa os vetores no banco `ChromaDB`
5. Recupera os trechos mais relevantes (*Top-K Similarity Search*) e aplica um **System Prompt com Guardrails de Fidelidade (*Faithfulness*)** para responder sem alucinações

---

## 🧠 Engenharia de Prompt & Experimentação com LLMs

Para garantir que o LLM (`llama3.2`) responda **exclusivamente** com base nos documentos recuperados e não invente dados fora da base, o pipeline utiliza o seguinte *Prompt Template* estruturado:

```text
Você é um assistente técnico especializado em análise documental.
Responda à pergunta do usuário utilizando EXCLUSIVAMENTE os trechos de contexto fornecidos abaixo.

REGRAS DE GROUNDING (GUARDRAILS):
1. Se a resposta não estiver explicitamente contida no contexto recuperado, responda exatamente:
   "Não encontrei informações suficientes nos documentos fornecidos para responder a esta pergunta."
2. Não invente, não extrapole e não utilize conhecimento prévio fora do contexto.
3. Cite o documento ou seção correspondente sempre que disponível nos metadados do chunk.

CONTEXTO RECUPERADO:
{context}

PERGUNTA DO USUÁRIO:
{question}
```

### Experimentações Realizadas
- **Embeddings (`mxbai-embed-large` — 1024 dims):** Melhor separação semântica em documentos técnicos em português quando comparado a embeddings menores.
- **Calibração de Chunking:** `chunk_size = 2000` caracteres com sobreposição (*overlap*) evitou perda de contexto em tabelas e cláusulas longas.

---

## 🛠️ Softwares e Bibliotecas Utilizadas

- **Python 3.12+**
- **Ollama** instalado e rodando ([Download](https://ollama.com/download))
- **Modelos do Ollama:** `mxbai-embed-large` (embeddings) e `llama3.2` (chat)

```bash
pip install python-dotenv langchain langchain-core langchain-ollama \
            langchain-community langchain-chroma chromadb ollama pypdf
```

---

## 📁 Estrutura do Projeto

```text
projeto/
├── base/                    # 📁 PDFs de entrada
│   ├── documento1.pdf
│   └── documento2.pdf
├── db/                      # Banco vetorial Chroma (gerado)
├── criar_db.ipynb           # Pipeline de ingestão, chunking e embeddings
├── main.ipynb               # Motor de busca semântica + Prompt RAG
└── README.md                # Documentação
```

---

## 🚀 Como Executar

1. Adicione seus arquivos PDF na pasta `base/`.
2. Execute o notebook `criar_db.ipynb` para processar os chunks e persistir os embeddings no `ChromaDB` (`db/`).
3. Execute o notebook `main.ipynb` e chame a função `perguntar()` para realizar consultas com *grounding* documental.

---

## 🔗 Links Úteis

- [Portfólio Completo no Notion](https://ember-bath-3fe.notion.site/Vitor-Fernandes-Gomes-2f935e03311980cda835e9a5455bf25d)
- [Ollama](https://ollama.com/) | [LangChain Docs](https://python.langchain.com/) | [Chroma DB](https://www.trychroma.com/)
