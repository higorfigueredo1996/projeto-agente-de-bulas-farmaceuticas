# Agente de Bulas Farmacêuticas (RAG com LangChain + Groq)

Agente de perguntas e respostas que usa **RAG (Retrieval-Augmented Generation)** para responder dúvidas de pacientes com base nas bulas oficiais de **Dipirona** e **Paracetamol**.

Projeto prático do curso [Arquiteturas de RAG com LLMs](https://www.alura.com.br) (Alura), adaptado para rodar com ferramentas gratuitas.

## Tecnologias

- **LangChain** (`langchain-classic`) — orquestração da cadeia RAG
- **Groq** (Llama 3.3 70B) — modelo de linguagem (LLM)
- **HuggingFace Embeddings** (`all-MiniLM-L6-v2`) — embeddings locais, sem custo de API
- **ChromaDB** — banco de dados vetorial
- **PyPDF** — extração de texto das bulas em PDF

## Como funciona

1. **Carregamento**: as duas bulas (PDF) são carregadas e cada página recebe um metadado indicando o medicamento de origem.
2. **Chunking**: os documentos são divididos em pedaços de 600 caracteres (com 150 de sobreposição), otimizado para o texto técnico das bulas.
3. **Categorização por metadados**: cada chunk é classificado por palavras-chave em uma seção da bula (indicação, contraindicação, posologia, reações adversas etc.).
4. **Indexação vetorial**: os chunks são convertidos em embeddings e armazenados no ChromaDB.
5. **Geração aumentada**: ao receber uma pergunta, o sistema busca os trechos mais relevantes e usa o Llama (via Groq) para gerar uma resposta baseada apenas nesse contexto.

## Descoberta: os limites da categorização por palavra-chave

Um dos experimentos do projeto comparou duas formas de busca para a mesma pergunta (*"Quais são as contraindicações da dipirona?"*):

| Busca | Resultado |
|---|---|
| **Sem filtro de categoria** (busca semântica pura) | Encontrou a resposta correta, mesmo com chunks mal categorizados (rotulados como `geral` ou `como_funciona`) |
| **Com filtro `categoria = contraindicacao`** | O agente respondeu "não sei" — só havia 1 chunk corretamente rotulado, e ele não continha informação suficiente |

**Conclusão**: a categorização por palavra-chave é rápida de implementar, mas frágil — um chunk pode conter a informação certa e ainda assim ser rotulado errado, porque a regra não entende *significado*, só *padrões de texto*. Filtrar por metadados aumenta a precisão quando a categorização está correta, mas pode descartar informação real quando não está. Esse é um trade-off central em sistemas RAG com metadados enriquecidos.

## Como rodar

1. Clone o repositório e abra o notebook no [Google Colab](https://colab.research.google.com).
2. Configure sua chave da [Groq](https://console.groq.com/keys) nos Secrets do Colab (`GROQ_API_KEY`).
3. Faça upload dos PDFs (`bula dipirona.pdf`, `bula paracetamol.pdf`) para o seu Google Drive.
4. Ajuste o caminho da pasta na célula correspondente.
5. Execute as células em ordem.

## Autor

[Higor Figueredo](https://github.com/higorfigueredo1996)

---
*⚠️ Projeto educacional. Não substitui orientação médica ou farmacêutica profissional.*
