# Resumo de técnicas de Engenharia de Prompt - Livro: Prompt Engineering - Author: Lee Boonstra

# Engenharia de Prompt: Guia Introdutório e Técnicas Fundamentais para LLMs

Este documento detalha os conceitos básicos, configurações essenciais e técnicas iniciais de engenharia de prompt ao trabalhar com Modelos de Linguagem Grandes (LLMs), como o Gemini, com base no material fornecido.

## Introdução: O Que é Prompt e Engenharia de Prompt?

*   **Prompt:** A entrada (geralmente texto, às vezes com imagens) fornecida a um LLM para direcionar sua saída.
*   **Quem pode fazer?** Qualquer pessoa pode escrever um prompt. Não é necessário ser cientista de dados ou engenheiro de ML.
*   **A Complexidade:** No entanto, criar um prompt *eficaz* é um desafio. A qualidade da resposta do LLM depende muito de:
    *   O modelo LLM específico.
    *   Seus dados de treinamento.
    *   As configurações do modelo (temperatura, etc.).
    *   A escolha das palavras, estilo, tom e estrutura do prompt.
    *   O contexto fornecido.
*   **Engenharia de Prompt:** É o **processo iterativo** de projetar, testar e refinar prompts para maximizar a precisão, relevância e utilidade das respostas do LLM. Prompts ruins geram resultados ruins.
*   **Foco (do Whitepaper Original):** Embora prompts sejam usados em interfaces de chatbot (como o Gemini Chat), este guia foca no uso via API ou plataformas como Vertex AI, que permitem controle fino sobre as **configurações do modelo**.

## Como os LLMs Funcionam (Visão Geral)

LLMs funcionam como **motores de previsão de tokens**. Dado um texto de entrada (prompt + resposta parcial), eles preveem a probabilidade de cada token possível no vocabulário ser o *próximo* token. O token mais provável (ou um escolhido com base nas configurações de amostragem) é adicionado à sequência, e o processo se repete. A engenharia de prompt visa "preparar o terreno" para que essa sequência de previsões leve ao resultado desejado.

## Tarefas Comuns Realizadas com Prompts

Prompts podem ser usados para diversas tarefas de compreensão e geração, como:
*   Resumo de texto
*   Extração de informações
*   Perguntas e Respostas (Q&A)
*   Classificação de texto
*   Tradução de idiomas ou código
*   Geração de código
*   Documentação de código
*   Raciocínio

## Configuração da Saída do LLM: Ajustando o Comportamento

Ajustar as configurações do modelo é tão importante quanto o texto do prompt:

1.  **Limite de Tokens de Saída (Output Length):**
    *   Define o número máximo de tokens na resposta.
    *   **Impacto:** Limites maiores = mais computação, custo, latência e consumo de energia.
    *   **Atenção:** Reduzir o limite *não* torna a resposta mais concisa automaticamente, apenas a corta. Para respostas curtas, o *prompt* também precisa ser ajustado.

2.  **Controles de Amostragem (Sampling Controls):**
    *   Controlam como o próximo token é selecionado a partir das probabilidades previstas.
    *   **Temperatura:**
        *   Controla a aleatoriedade.
        *   `Baixa (~0)`: Mais determinístico, focado, factual. Ideal para respostas únicas/corretas (ex: matemática). `Temp=0` é "greedy decoding" (escolhe sempre o mais provável).
        *   `Alta (~1+)`: Mais diverso, criativo, inesperado. Aumenta a chance de respostas aleatórias.
    *   **Top-K:**
        *   Considera apenas os `K` tokens mais prováveis.
        *   `Baixo K`: Mais restritivo. `K=1` é determinístico.
        *   `Alto K`: Mais criativo.
    *   **Top-P (Nucleus Sampling):**
        *   Considera o menor conjunto de tokens cuja probabilidade *acumulada* atinge `P`.
        *   `Baixo P (~0)`: Mais determinístico.
        *   `Alto P (~1)`: Mais diverso (`P=1` considera todos com prob > 0).

**Interação e Recomendações:**
*   Essas configurações **interagem**:
    *   `Temp=0` ou `Top-K=1` tornam as outras irrelevantes (resposta determinística).
    *   `Top-P=1` ou `Top-K` muito alto dão mais peso à `Temperatura`.
*   **Experimentação é chave.**
*   **Pontos de Partida Sugeridos:**
    *   *Coerente/Levemente Criativo:* Temp `0.2`, Top-P `0.95`, Top-K `30`.
    *   *Muito Criativo:* Temp `0.9`, Top-P `0.99`, Top-K `40`.
    *   *Pouco Criativo/Factual:* Temp `0.1`, Top-P `0.9`, Top-K `20`.
*   **⚠️ Cuidado com Loops de Repetição:** Configurações inadequadas (muito baixas *ou* muito altas) podem fazer o modelo repetir palavras/frases. Ajustar esses parâmetros é crucial para evitar isso.

## Técnicas Básicas de Prompting

1.  **Zero-Shot Prompting:**
    *   A forma mais simples: apenas a descrição da tarefa ou a pergunta, **sem exemplos**.
    *   Exemplo (Classificação):
        ```
        Classify movie reviews as POSITIVE, NEUTRAL or NEGATIVE.
        Review: "Her" is a disturbing study... I wish there were more movies like this masterpiece.
        Sentiment:
        ```
        *Saída Esperada:* `POSITIVE`

2.  **One-Shot & Few-Shot Prompting:**
    *   Fornecer **um (one-shot)** ou **vários (few-shot)** exemplos completos (entrada + saída desejada) no prompt.
    *   **Quando usar:** Quando zero-shot não funciona bem, ou para guiar o modelo a um formato/padrão de saída específico.
    *   **Quantos exemplos?** Geralmente 3-5 para few-shot, mas depende da complexidade da tarefa e do modelo.
    *   **Qualidade dos Exemplos:** Devem ser relevantes, diversos, bem escritos e *corretos*. Um erro no exemplo confunde o modelo. Inclua *edge cases* para robustez.
    *   Exemplo (Few-Shot para JSON de Pizza):
        ```
        Parse a customer's pizza order into valid JSON:
        EXAMPLE:
        I want a small pizza with cheese, tomato sauce, and pepperoni.
        JSON Response:
        ```json
        { "size": "small", "type": "normal", "ingredients": [["cheese", "tomato sauce", "peperoni"]] }
        ```
        EXAMPLE:
        Can I get a large pizza with tomato sauce, basil and mozzarella
        ```json
        { "size": "large", "type": "normal", "ingredients": [["tomato sauce", "bazel", "mozzarella"]] }
        ```
        Now, I would like a large pizza, with the first half cheese and mozzarella. And the other tomato sauce, ham and pineapple.
        JSON Response:
        ```
        *Saída Esperada:* JSON para a pizza meio a meio.

3.  **System, Contextual & Role Prompting:** Técnicas para guiar o LLM com informações meta:

    *   **System Prompting:** Define regras gerais, o propósito fundamental, ou restrições de formato/comportamento para a interação.
        *   Exemplo 1 (Formato): `"Classify movie reviews... **Only return the label in uppercase.**"`
        *   Exemplo 2 (JSON): `"Classify movie reviews... **Return valid JSON:** ... Schema: ... JSON Response:"` (Força estrutura, limita alucinações).
        *   Exemplo 3 (Segurança): `"**You should be respectful in your answer.**"`
    *   **Role Prompting:** Atribui uma persona, identidade ou profissão específica ao LLM, influenciando tom, estilo e conhecimento focado.
        *   Exemplo (Guia de Viagem): `"I want you to act as a travel guide. I will write to you about my location and you will suggest 3 places to visit near me... My suggestion: 'I am in Amsterdam and I want to visit only museums.' Travel Suggestions:"`
        *   **Estilos:** Pode-se especificar estilos como `Humorous`, `Formal`, `Inspirational`, `Persuasive`, `Descriptive`, `Direct`, etc.
        *   Exemplo (Guia com Humor): `"I want you to act as a travel guide... suggest 3 places to visit near me in a **humorous style**. My suggestion: 'I am in Manhattan.' Travel Suggestions:"`
    *   **Contextual Prompting:** Fornece informações de fundo específicas para a *tarefa atual*, ajudando o modelo a entender nuances e gerar respostas mais relevantes.
        *   Exemplo (Blog Retrô): `"**Context: You are writing for a blog about retro 80's arcade video games.** Suggest 3 topics to write an article about..."`

    **Distinção e Sobreposição:** Embora possam se misturar (um prompt de função pode ter contexto), o foco principal difere:
    *   **System:** Regras e propósito geral.
    *   **Contextual:** Informação específica da tarefa imediata.
    *   **Role:** Personalidade, tom e estilo da saída.

## Documentação e Iteração

*   O texto original ressalta a importância de **documentar as tentativas de prompt**, usando um formato estruturado (como as tabelas nos exemplos), pois a engenharia de prompt é um processo **iterativo** de tentativa e erro.

---

## 1. Prompting com Passo Atrás (Step-back Prompting)

**O que é?**
É uma técnica onde, antes de pedir ao LLM para resolver uma tarefa específica, você primeiro faz uma pergunta mais geral ou sobre princípios relacionados. A resposta a essa pergunta geral é então fornecida como contexto no prompt final para a tarefa específica.

**Por que usar?**
*   **Ativa Conhecimento Relevante:** Ajuda o LLM a acessar e utilizar uma base de conhecimento mais ampla e princípios gerais antes de focar nos detalhes.
*   **Melhora a Qualidade:** Ao considerar o quadro geral, o LLM pode gerar respostas mais precisas, criativas e perspicazes para a tarefa específica.
*   **Reduz Vieses:** Focar em princípios gerais pode ajudar a mitigar vieses que poderiam surgir ao focar apenas em detalhes específicos.

**Como funciona (Exemplo Conceitual):**

1.  **Prompt Direto (Resultado potencialmente genérico):**
    `"Escreva um enredo para um nível de jogo de tiro em primeira pessoa."`

2.  **Prompt de Passo Atrás (Pergunta Geral):**
    `"Quais são 5 temas/cenários comuns e envolventes em jogos de tiro em primeira pessoa?"`
    *   *Saída Esperada:* Uma lista de temas como "Base Militar Abandonada", "Cidade Cyberpunk", etc.

3.  **Prompt Final (Combinado):**
    `Contexto: [Lista de temas gerada no passo 2]`
    `"Usando um desses temas, escreva um enredo de um parágrafo para um nível de jogo de tiro em primeira pessoa que seja desafiador e envolvente."`
    *   *Saída Esperada:* Um enredo mais específico e criativo, baseado em um dos temas fornecidos.

**Benefício Principal:** Aumenta a relevância e a profundidade da resposta final, aproveitando melhor o conhecimento interno do LLM.

## 2. Cadeia de Pensamento (Chain of Thought - CoT)

**O que é?**
É uma técnica que instrui explicitamente o LLM a "pensar passo a passo" ou a detalhar seu processo de raciocínio antes de fornecer a resposta final. Isso é geralmente feito adicionando frases como "Vamos pensar passo a passo." ao final do prompt.

**Por que usar?**
*   **Melhora o Raciocínio:** Particularmente eficaz para tarefas que exigem lógica, matemática ou raciocínio complexo, onde LLMs podem falhar se tentarem responder diretamente.
*   **Interpretabilidade:** Permite ver *como* o LLM chegou à resposta, facilitando a identificação de erros no processo de raciocínio.
*   **Robustez:** Ajuda a manter a performance do prompt mais consistente entre diferentes versões ou modelos de LLMs.

**Como funciona (Exemplo Conceitual - Problema Matemático):**

1.  **Prompt Direto (Resultado potencialmente errado):**
    `"Quando eu tinha 3 anos, meu parceiro tinha o triplo da minha idade. Agora tenho 20. Quantos anos tem meu parceiro?"`
    *   *Saída Possível:* `60` ou `63` (Incorreto)

2.  **Prompt CoT (Zero-Shot):**
    `"Quando eu tinha 3 anos, meu parceiro tinha o triplo da minha idade. Agora tenho 20. Quantos anos tem meu parceiro? Vamos pensar passo a passo."`
    *   *Saída Esperada:*
        `1. Quando eu tinha 3, o parceiro tinha 3*3 = 9 anos.`
        `2. A diferença de idade é 9 - 3 = 6 anos.`
        `3. Agora eu tenho 20 anos.`
        `4. O parceiro também envelheceu (20 - 3) = 17 anos desde aquela época.`
        `5. Idade atual do parceiro = 9 + 17 = 26 anos.`
        `Ou: Idade atual do parceiro = Minha idade atual + diferença = 20 + 6 = 26 anos.`
        `Resposta: 26`

**Variações:**
*   **Zero-Shot CoT:** Simplesmente adicionar a instrução "pense passo a passo".
*   **Few-Shot CoT:** Fornecer um ou mais exemplos (Pergunta + Raciocínio Passo a Passo + Resposta) no prompt antes da pergunta final, para guiar o LLM sobre como estruturar seu raciocínio.

**Desvantagens:**
*   **Custo/Latência:** Gera respostas mais longas (mais tokens), o que pode aumentar o custo e o tempo de resposta.

**Benefício Principal:** Aumenta significativamente a precisão em tarefas que requerem raciocínio sequencial e torna o processo transparente.

---

Ambas as técnicas são ferramentas valiosas na engenharia de prompt para extrair o melhor desempenho dos LLMs, seja garantindo que o conhecimento relevante seja ativado (Passo Atrás) ou guiando o processo de raciocínio (Cadeia de Pensamento).

---

# Mais Técnicas Avançadas de Prompting para LLMs

Este documento detalha três técnicas adicionais de engenharia de prompt para aprimorar as capacidades dos Modelos de Linguagem Grandes (LLMs): **Autoconsistência (Self-consistency)**, **Árvore de Pensamentos (Tree of Thoughts - ToT)** e **ReAct (Raciocinar e Agir)**.

## 1. Autoconsistência (Self-consistency)

**O que é?**
É uma técnica que aprimora a Cadeia de Pensamento (CoT). Em vez de gerar uma única sequência de raciocínio, a Autoconsistência executa o mesmo prompt (geralmente um prompt CoT) várias vezes, com uma configuração de *temperatura* mais alta para encorajar diversidade nas respostas. A resposta final é determinada pela **votação majoritária** entre as conclusões dos diferentes caminhos de raciocínio gerados.

**Por que usar?**
*   **Melhora a Precisão:** Especialmente útil em tarefas de raciocínio onde a "decodificação gulosa" do CoT padrão pode levar a erros. A agregação de múltiplas respostas aumenta a chance de chegar à resposta correta.
*   **Aumenta a Robustez:** Torna a resposta final menos dependente de um único caminho de raciocínio, que pode ser falho.
*   **Trata Ambiguidade:** Pode ajudar a lidar com prompts que podem ser interpretados de maneiras diferentes (como o exemplo do e-mail com sarcasmo).

**Como funciona (Exemplo Conceitual - Classificação de E-mail):**

1.  **Prompt (CoT):** Dê ao LLM um e-mail ambíguo (ex: um relato de bug com tom sarcástico) e peça para classificar como IMPORTANTE/NÃO IMPORTANTE, explicando o passo a passo.
2.  **Múltiplas Execuções (com Temperatura Alta):** Execute o prompt várias vezes (ex: 3 vezes).
    *   *Tentativa 1:* Raciocínio focado no risco técnico -> Conclusão: IMPORTANTE.
    *   *Tentativa 2:* Raciocínio focado no tom casual e falta de urgência -> Conclusão: NÃO IMPORTANTE.
    *   *Tentativa 3:* Raciocínio similar à Tentativa 1 -> Conclusão: IMPORTANTE.
3.  **Votação Majoritária:** Conte as conclusões: IMPORTANTE (2 votos), NÃO IMPORTANTE (1 voto).
4.  **Resposta Final:** IMPORTANTE.

**Considerações:**
*   **Custo Computacional:** Requer múltiplas inferências do LLM para um único prompt, aumentando o custo e o tempo de resposta.

## 2. Árvore de Pensamentos (Tree of Thoughts - ToT)

**O que é?**
É uma generalização da Cadeia de Pensamento que permite ao LLM explorar **múltiplos caminhos de raciocínio simultaneamente**, de forma não linear, como os ramos de uma árvore. Em vez de seguir um único fio de pensamento, o ToT pode gerar várias "continuações" ou "ideias" em cada etapa do raciocínio e avaliá-las, decidindo quais ramos explorar mais a fundo.

**Por que usar?**
*   **Tarefas Complexas com Exploração:** Ideal para problemas que não têm um caminho de solução linear óbvio e que podem se beneficiar da exploração de diferentes possibilidades ou estratégias (ex: planejamento, escrita criativa com restrições, alguns problemas matemáticos).
*   **Supera Limitações do CoT:** O CoT pode ficar preso em um caminho de raciocínio incorreto. O ToT permite "voltar atrás" e explorar alternativas.

**Como funciona (Conceitual):**

1.  **Problema Inicial:** Ex: "Escreva um conto de 200 palavras sobre um robô que descobre a música."
2.  **Geração de Pensamentos (Nível 1):** O LLM gera várias ideias iniciais ou abordagens:
    *   *Pensamento A:* Focar na reação inicial do robô ao som.
    *   *Pensamento B:* Focar em como o robô tenta analisar a música logicamente.
    *   *Pensamento C:* Focar na interação do robô com humanos sobre música.
3.  **Avaliação e Expansão (Nível 2):** O LLM (ou um mecanismo de avaliação) avalia quais pensamentos são mais promissores. Expande os melhores:
    *   *De A:* Detalhar a "confusão sensorial" do robô.
    *   *De B:* Descrever as tentativas falhas de categorizar a música.
4.  **Continuação:** O processo se repete, construindo uma árvore de sequências de pensamento interconectadas até que uma solução satisfatória seja encontrada.

**Considerações:**
*   **Complexidade de Implementação:** Mais complexo de implementar e gerenciar do que CoT ou Autoconsistência.
*   **Potencialmente Custoso:** A exploração de muitos ramos pode ser computacionalmente intensiva.

## 3. ReAct (Raciocinar e Agir)

**O que é?**
É um paradigma que combina o **raciocínio** interno do LLM com a capacidade de **agir** usando **ferramentas externas** (como APIs de busca, calculadoras, interpretadores de código). O LLM opera em um ciclo: **Pensamento -> Ação -> Observação**.

**Por que usar?**
*   **Acesso a Conhecimento Atualizado/Externo:** Permite que o LLM supere limitações de seu conhecimento interno (que é estático até o corte de treinamento) buscando informações em tempo real.
*   **Realizar Cálculos Complexos:** Pode usar ferramentas como calculadoras ou Python para tarefas matemáticas onde LLMs puros podem falhar.
*   **Interagir com o Mundo:** Pode interagir com APIs para obter dados específicos, controlar dispositivos (em cenários mais avançados), etc.
*   **Tarefas Multi-etapas Complexas:** Ideal para tarefas que exigem coletar informações de várias fontes ou realizar ações intermediárias para chegar à resposta final (como no exemplo do Metallica).

**Como funciona (Ciclo Pensamento-Ação-Observação - Exemplo Metallica):**

1.  **Prompt:** "Quantos filhos os membros da banda Metallica têm?"
2.  **Pensamento 1:** "Preciso saber quem são os membros e quantos filhos cada um tem."
3.  **Ação 1:** Usar a ferramenta `Search("Membros da banda Metallica")`.
4.  **Observação 1:** "Os membros são Hetfield, Ulrich, Hammett, Trujillo."
5.  **Pensamento 2:** "Ok, 4 membros. Preciso saber os filhos de Hetfield."
6.  **Ação 2:** Usar a ferramenta `Search("Quantos filhos tem James Hetfield?")`.
7.  **Observação 2:** "3 filhos."
8.  **Pensamento 3:** "Total 3. Próximo: Ulrich."
9.  **Ação 3:** Usar a ferramenta `Search("Quantos filhos tem Lars Ulrich?")`.
10. **Observação 3:** "3 filhos."
11. **Pensamento 4:** "Total 3+3=6. Próximo: Hammett."
12. *(Ciclo continua para Hammett e Trujillo)*
13. **Pensamento Final:** "Somei todos: 3+3+2+2 = 10."
14. **Resposta Final:** 10.

**Considerações:**
*   **Necessidade de Ferramentas:** Requer a configuração e integração de ferramentas externas (APIs, etc.).
*   **Gerenciamento de Contexto:** É preciso gerenciar o histórico de pensamentos, ações e observações para manter o LLM no caminho certo.
*   **Potencial para Erros:** O LLM pode escolher a ferramenta errada, interpretar mal a observação ou entrar em loops.

---

Estas técnicas representam avanços significativos na engenharia de prompt, permitindo que os LLMs abordem problemas mais complexos, acessem informações externas e produzam resultados mais confiáveis e bem fundamentados.

---
