# Melhores Práticas em Engenharia de Prompt

## Descrição rápida:
> Esse documento foi traduzido dos últimos capítulos (com ajuda da Gemini 2.5 Pro) do livro: Prompt Engineering do curso 5-day Gen AI da Google com a Kaggle.
> Atenção: A engenharia de prompt é um processo fundamentalmente iterativo, exigindo experimentação contínua (tinkering) para encontrar a formulação ideal. Adotar as melhores práticas a seguir é essencial para aprimorar sua eficácia nessa tarefa. Um dos pilares desse processo é a documentação rigorosa: registre cada tentativa, anotando as alterações, a qualidade da resposta da IA (se melhorou, piorou ou se tornou genérica) e outras observações relevantes. Lembre-se também de priorizar instruções positivas (o que fazer) em vez de acumular restrições negativas (o que não fazer), pois um excesso de limitações pode confundir ou restringir indevidamente o modelo..

## Forneça Exemplos (One-shot / Few-shot)

Esta é talvez a prática mais crucial. Fornecer exemplos no prompt atua como uma poderosa ferramenta de ensino.

*   **Por quê?** Os exemplos mostram a saída desejada ou respostas similares, permitindo ao modelo aprender e adaptar sua geração. Funciona como dar um ponto de referência ou um alvo.
*   **Benefícios:** Melhora a precisão, estilo e tom da resposta para melhor corresponder às suas expectativas.

## Projete com Simplicidade

Prompts devem ser concisos, claros e fáceis de entender, tanto para você quanto para o modelo.

*   **Regra geral:** Se já é confuso para você, provavelmente será confuso para o modelo.
*   **Evite:** Linguagem complexa, informações desnecessárias.

*   **Exemplo de Refinamento:**

    *   **ANTES:**
        ```
        Estou visitando Nova York agora e gostaria de ouvir mais sobre ótimos locais. Estou com duas crianças de 3 anos. Para onde devemos ir durante nossas férias?
        ```
    *   **DEPOIS DE REESCRITO:**
        ```
        Aja como um guia de viagens para turistas. Descreva ótimos lugares para visitar em Manhattan, Nova York, com uma criança de 3 anos.
        ```

*   **Use Verbos de Ação:** Prefira verbos que descrevam claramente a ação desejada. Exemplos:
    *   `Agir`, `Analisar`, `Categorizar`, `Classificar`, `Contrastar`, `Comparar`, `Criar`, `Descrever`, `Definir`, `Avaliar`, `Extrair`, `Encontrar`, `Gerar`, `Identificar`, `Listar`, `Medir`, `Organizar`, `Analisar sintaticamente (Parse)`, `Escolher`, `Prever`, `Fornecer`, `Classificar (Rank)`, `Recomendar`, `Retornar`, `Recuperar`, `Reescrever`, `Selecionar`, `Mostrar`, `Ordenar`, `Resumir`, `Traduzir`, `Escrever`.

## Seja Específico Sobre a Saída

Instruções vagas podem levar a resultados genéricos ou inadequados. Fornecer detalhes específicos ajuda o modelo a focar no que é relevante.

*   **Exemplo:**

    *   **FAÇA:**
        ```
        Gere uma postagem de blog de 3 parágrafos sobre os 5 principais consoles de videogame. A postagem do blog deve ser informativa e envolvente, e deve ser escrita em um estilo conversacional.
        ```
    *   **NÃO FAÇA:**
        ```
        Gere uma postagem de blog sobre consoles de videogame.
        ```

## Use Instruções em Vez de Restrições (Quando Possível)

*   **Instrução:** Diz ao modelo *o que fazer* (formato, estilo, conteúdo).
*   **Restrição:** Diz ao modelo *o que não fazer* ou evitar.

Pesquisas sugerem que focar em **instruções positivas** é geralmente mais eficaz:

*   **Alinhamento Humano:** Preferimos ouvir o que fazer em vez de uma lista de proibições.
*   **Clareza:** Instruções comunicam diretamente o objetivo. Restrições podem deixar o modelo adivinhando o que *é* permitido.
*   **Flexibilidade:** Instruções permitem criatividade dentro dos limites. Restrições podem limitar excessivamente.
*   **Conflitos:** Múltiplas restrições podem entrar em conflito.

**Quando usar Restrições:**
*   Para evitar conteúdo prejudicial/tendencioso.
*   Quando um formato/estilo de saída *estrito* é absolutamente necessário.

**Prática Recomendada:** Priorize instruções (diga o que fazer). Use restrições apenas quando necessário para segurança, clareza ou requisitos específicos.

*   **Exemplo:**

    *   **FAÇA (Instruções Positivas):**
        ```
        Gere uma postagem de blog de 1 parágrafo sobre os 5 principais consoles de videogame. Discuta apenas o console, a empresa que o fez, o ano e as vendas totais.
        ```
    *   **NÃO FAÇA (Foco em Restrições):**
        ```
        Gere uma postagem de blog de 1 parágrafo sobre os 5 principais consoles de videogame. Não liste nomes de videogames.
        ```

Experimente e itere com diferentes combinações de instruções e restrições, documentando os resultados.

## Controle o Comprimento Máximo de Tokens

Você pode controlar o tamanho da resposta de duas formas:

1.  **Configuração do Modelo:** Definir o parâmetro `Limite de Token` (ou similar).
2.  **Instrução no Prompt:** Solicitar explicitamente um comprimento.
    *   Exemplo: `"Explique física quântica em uma mensagem do tamanho de um tweet."`

## Use Variáveis em Prompts

Para reutilização e dinamismo, especialmente ao integrar prompts em aplicações:

*   Defina variáveis (placeholders) no seu prompt.
*   Substitua essas variáveis com valores específicos em tempo de execução.
*   Evita repetição e facilita a manutenção.

*   **Exemplo (Tabela 20):**

    *   **VARIÁVEIS:**
        ```
        {city} = "Amsterdam"
        ```
    *   **PROMPT:**
        ```
        Você é um guia de viagens. Diga-me um fato sobre a cidade: {city}
        ```
    *   **Saída:** (Um fato sobre Amsterdã)

## Experimente com Formatos de Entrada e Estilos de Escrita

Resultados variam com modelos, configurações, formato do prompt e escolha de palavras. Experimente diferentes abordagens:

*   **Estilo:** Formal, informal, humorístico, etc.
*   **Escolha de Palavras:** Sinônimos, verbos específicos.
*   **Tipo de Prompt:** Zero-shot, few-shot, system, role, etc.
*   **Formulação:** Pergunta, declaração, instrução.

*   **Exemplo (Sega Dreamcast):**
    *   **Pergunta:** `O que foi o Sega Dreamcast e por que ele foi um console tão revolucionário?`
    *   **Declaração:** `O Sega Dreamcast foi um console de videogame de sexta geração...`
    *   **Instrução:** `Escreva um único parágrafo que descreva o console Sega Dreamcast e explique por que ele foi tão revolucionário.`
    *(Cada formulação pode levar a uma resposta ligeiramente diferente)*

## Para Few-Shot com Classificação: Misture as Classes

Ao usar exemplos `few-shot` para tarefas de classificação:

*   **Não** coloque todos os exemplos de uma classe juntos e depois todos da outra.
*   **Misture** os exemplos das diferentes classes.
*   **Por quê?** Evita que o modelo aprenda a ordem em vez das características distintivas de cada classe (*overfitting* à sequência). Leva a um desempenho mais robusto e generalizável.
*   **Regra geral:** Comece com cerca de 6 exemplos `few-shot` (misturados) e teste a precisão.

## Adapte-se às Atualizações do Modelo

Modelos evoluem (arquitetura, dados, capacidades).

*   Mantenha-se informado sobre as mudanças.
*   Teste versões mais recentes dos modelos.
*   Ajuste seus prompts para aproveitar novos recursos.
*   Use ferramentas (como Vertex AI Studio) para armazenar, testar e versionar prompts.

## Experimente com Formatos de Saída (JSON, XML)

Para tarefas **não criativas** (extração, classificação, ordenação, etc.), considere solicitar a saída em formatos estruturados.

*   **Benefícios do JSON (já mencionados):**
    *   Estilo consistente.
    *   Foco nos dados desejados.
    *   Menor chance de alucinações (força estrutura).
    *   Pode representar relacionamentos.
    *   Permite tipos de dados explícitos.
    *   Pode ser pré-ordenado.
    *(Referência à Tabela 4 como exemplo)*

*   **JSON Repair:**
    *   **Problema:** JSON requer mais tokens e pode ser truncado (invalidado) se o limite de saída for atingido.
    *   **Solução:** Use bibliotecas como `json-repair` (PyPI) para tentar corrigir automaticamente JSON malformado ou incompleto gerado por LLMs.

## Trabalhando com Schemas (Entrada Estruturada)

Assim como JSON é bom para a *saída*, **JSON Schema** é útil para estruturar a *entrada*.

*   **O que é:** Define a estrutura e os tipos de dados esperados para uma entrada JSON.
*   **Benefícios:**
    *   Dá ao LLM um "mapa" claro dos dados de entrada.
    *   Ajuda a focar nas informações relevantes.
    *   Reduz risco de má interpretação da entrada.
    *   Pode definir relacionamentos e formatos específicos (como datas).

*   **Exemplo (Catálogo de E-commerce):**

    1.  **Definir o Schema (Snippet 5):**
        ```json
        {
         "type": "object",
         "properties": {
          "name": { "type": "string", "description": "Nome do produto" },
          "category": { "type": "string", "description": "Categoria do produto" },
          "price": { "type": "number", "format": "float", "description": "Preço do produto" },
          "features": {
           "type": "array",
           "items": { "type": "string" },
           "description": "Características chave do produto"
          },
          "release_date": { "type": "string", "format": "date", "description": "Data de lançamento do produto"}
         },
         "required": ["name", "category", "price"]
        }
        ```
    2.  **Fornecer Dados Conformes ao Schema (Snippet 6 - *Entrada* para o LLM):**
        ```json
        {
         "name": "Fones de Ouvido Sem Fio",
         "category": "Eletrônicos",
         "price": 99.99,
         "features": ["Cancelamento de ruído", "Bluetooth 5.0", "Bateria de 20 horas"],
         "release_date": "2023-10-27"
        }
        ```
    *   **Resultado:** Ao fornecer *ambos*, schema e dados, o LLM entende melhor os atributos e gera descrições mais precisas, especialmente útil com grandes volumes de dados.

## Experimente Junto com Outros Engenheiros de Prompt

Se a tarefa de criar um bom prompt for desafiadora, envolva várias pessoas. Seguindo as melhores práticas, a diversidade de abordagens pode levar a descobrir prompts com melhor desempenho.

## Melhores Práticas Específicas de CoT (Chain of Thought)

*   **Ordem:** Coloque a **resposta final** *depois* da sequência de raciocínio. A geração do raciocínio afeta a previsão da resposta.
*   **Extração:** Garanta que seja possível extrair programaticamente a resposta final, separada do texto do raciocínio (importante para CoT + Autoconsistência).
*   **Temperatura:** Defina a **Temperatura como 0**. Como o CoT visa um raciocínio lógico para (geralmente) uma única resposta correta, a decodificação determinística (greedy) é preferível.

## Documente as Várias Tentativas de Prompt

**Extremamente importante!** Documente suas tentativas em detalhes.

*   **Por quê?**
    *   Saídas variam entre modelos, versões, configurações e até execuções idênticas (devido a quebras de empate aleatórias).
    *   Ajuda a lembrar o que funcionou/não funcionou ao revisitar o trabalho.
    *   Facilita o teste em novas versões de modelos.
    *   Auxilia na depuração de erros futuros.

*   **Modelo de Documentação Sugerido (Tabela 21):** Use uma planilha (Google Sheet) ou similar.

    | Campo         | Descrição                                          |
    | :------------ | :------------------------------------------------- |
    | **Nome**      | `[nome e versão do seu prompt]`                    |
    | **Objetivo**  | `[Explicação de uma frase do objetivo desta tentativa]` |
    | **Modelo**    | `[nome e versão do modelo usado]`                  |
    | **Temperatura** | `[valor entre 0 - 1]`                            |
    | **Limite Token**| `[número]`                                       |
    | **Top-K**     | `[número]`                                       |
    | **Top-P**     | `[número]`                                       |
    | **Prompt**    | `[Escreva todo o prompt completo]`                 |
    | **Saída**     | `[Escreva a saída ou múltiplas saídas]`            |

*   **Campos Adicionais Úteis:**
    *   Versão/Iteração do Prompt.
    *   Resultado (OK / NÃO OK / ÀS VEZES OK).
    *   Feedback/Notas.
    *   Link para o prompt salvo (se usar ferramentas como Vertex AI Studio).
    *   Para sistemas RAG: Detalhes da consulta, chunks, etc.

*   **Integração e Testes:**
    *   Quando o prompt estiver bom, mova-o para a base de código.
    *   Salve prompts separadamente do código para facilitar a manutenção.
    *   Idealmente, use testes automatizados e avaliação contínua para monitorar o desempenho do prompt em um sistema operacionalizado.

**Lembre-se: Engenharia de prompt é um processo iterativo.** Crie, teste, analise, documente, refine. Continue experimentando, especialmente ao mudar modelos ou configurações.
