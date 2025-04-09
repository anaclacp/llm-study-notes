# Livro 2 - Embeddings

## Capítulo: Why embeddings are important

Essencialmente, *embeddings* são representações numéricas de dados do mundo real, como textos, fala, imagens ou vídeos. O nome *embeddings* se refere a um conceito semelhante na matemática, onde um espaço pode ser mapeado — ou *embutido* — em outro espaço. 

Por exemplo, o modelo BERT original transforma um texto em um vetor de 768 números, fazendo um mapeamento de um espaço altamente dimensional (como o de todas as sentenças possíveis) para um espaço muito menor de 768 dimensões.

Esses *embeddings* são expressos como vetores de baixa dimensão, onde a distância entre dois vetores representa a relação e a similaridade semântica entre os objetos reais que eles representam.

Eles ajudam a:
- Fornecer representações compactas de diferentes tipos de dados.
- Comparar objetos distintos numericamente quanto à similaridade.

Exemplo: a palavra “computador” tem significado semelhante à imagem de um computador ou à palavra “laptop”, mas não à palavra “carro”.

Essas representações ajudam no processamento e armazenamento eficiente de dados em larga escala, funcionando como uma forma de compressão com perdas, mas mantendo informações semânticas importantes.

---

### Aplicações-Chave

As principais aplicações de *embeddings* estão em sistemas de busca (*retrieval*) e recomendação, onde os resultados são extraídos de um espaço de busca massivo.

O sucesso desses sistemas depende de:

1. Pré-computar os *embeddings* de bilhões de itens no espaço de busca.
2. Mapear a consulta para o mesmo espaço de *embedding*.
3. Recuperar de forma eficiente os itens mais próximos da consulta no espaço vetorial.

---

### Embeddings e Multimodalidade

Os *embeddings* também se destacam em contextos multimodais — onde há dados de diferentes tipos, como texto, fala, imagem e vídeo.

Um exemplo disso são os **joint embeddings** (*embeddings conjuntos*), que mapeiam diferentes tipos de dados para o mesmo espaço vetorial. Exemplo: buscar vídeos com base em uma consulta em texto.

Essas representações são projetadas para capturar o máximo possível das características originais dos objetos.

---

### Embeddings Preservam Significados

Os *embeddings* são projetados para posicionar objetos com significados semelhantes mais próximos uns dos outros em um espaço vetorial de baixa dimensão. Isso permite que sejam usados como entradas significativas e condensadas em aplicações posteriores (*downstream tasks*).

Além disso, essas representações mantêm os significados semânticos para tarefas específicas (ou múltiplas). Isso significa que:

- A **mesma palavra ou objeto pode ter diferentes embeddings**, dependendo da tarefa.
- Por exemplo: a palavra "maçã" terá embeddings diferentes em frases como “maçã é uma fruta” e “maçã é uma marca de tecnologia”.

---
