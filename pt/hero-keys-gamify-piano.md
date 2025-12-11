---
title: "HeroKeys: Gamificando o Aprendizado de Piano com IA e a API do Moises"
date: "2025-12-11"
excerpt: "Como construí uma plataforma open-source que une processamento de áudio, gamificação estilo Guitar Hero e desafios de deploy de modelos de ML na nuvem."
category: "Engenharia de Machine Learning"
tags: ["Machine Learning", "Music Tech", "Next.js", "Moises API", "Web Audio"]
coverImage: "https://github.com/JoaquimBreno/blog-posts/blob/main/imgs/hero-keys.png?raw=true"
author: "Joaquim Breno"
authorImage: "https://avatars.githubusercontent.com/u/62159887?v=4"
authorBio: "Machine Learning Engineer"
---

# HeroKeys: Gamificando o Aprendizado de Piano com IA

## Um "Guitar Hero" para quem quer aprender Piano com sua música favorita

![Hero Keys Banner](https://github.com/JoaquimBreno/blog-posts/blob/main/imgs/hero-keys.png?raw=true)

Aprender um instrumento musical é uma jornada incrível, mas a curva de aprendizado inicial pode ser frustrante. A dependência de partituras complexas ou o treino auditivo (que iniciantes ainda não têm) cria uma barreira de entrada enorme.

Como proposta de um projeto de hackathon, decidi junto a minha equipe desenvolver o **HeroKeys**. A ideia era simples, mas ambiciosa: criar uma aplicação web que permitisse ao usuário fazer upload de *qualquer* música, e o sistema "ouviria" o piano, transcreveria as notas e criaria um jogo interativo para praticar com um teclado real.

Neste post, vou detalhar a arquitetura do projeto, como integrei a API do Moises e, o mais importante, os desafios reais de colocar modelos de áudio em produção com recursos limitados.

### A Arquitetura e a "Mágica" do Moises.ai

O coração do HeroKeys é a capacidade de isolar instrumentos e entender o que está sendo tocado. Para isso, utilizei a poderosa **API do Moises.ai**.

O fluxo funciona da seguinte maneira:

1.  **Frontend (Next.js):** O usuário envia o áudio.
2.  **Separação de Fontes (Source Separation):** O áudio é enviado para o endpoint de separação do Moises, que utiliza Deep Learning para extrair apenas a faixa de piano (o famoso *stem*), removendo bateria, voz e baixo.
3.  **Reconhecimento de Acordes:** Paralelamente, utilizamos o módulo de detecção de acordes do Moises para identificar a harmonia da música em tempo real.

Sem essa etapa de separação, a transcrição das notas seria extremamente ruidosa e imprecisa.


### A Experiência do Usuário e Visualização

Para a interface, me inspirei na mecânica consagrada de jogos de ritmo. Desenvolvi um **Piano Roll** onde as notas descem em sincronia com o áudio.

![Interface do HeroKeys com Piano Roll](https://github.com/JoaquimBreno/blog-posts/blob/main/imgs/piano_roll_acordes_juice.png?raw=true)

O sistema suporta:
* **Conexão MIDI via USB:** Você conecta seu teclado físico e o navegador captura o que você toca.
* **Feedback em Tempo Real:** Se você acertar a nota no tempo certo, ganha pontos e mantém o *streak* (multiplicador de pontuação).
* **Visualização de Partitura:** Para quem prefere a leitura tradicional, o sistema também renderiza a pauta musical.

### O Desafio da Transcrição e Limitações de Deploy (A Vida Real)

Aqui entra a parte da "vida real" de um Engenheiro de ML. No paper original do projeto, a arquitetura ideal incluía um backend Python (FastAPI) rodando modelos pesados de transcrição (como o *High-resolution Piano Transcription* ou o *Basic Pitch* do Spotify).

No entanto, colocar isso em produção "no budget" é um desafio técnico interessante.

Tentei subir o servidor de inferência no **Render** (plataforma de cloud application). O problema? Modelos de transcrição de áudio baseados em CNNs ou Transformers consomem uma quantidade considerável de memória apenas para carregar os pesos, sem contar o processamento do áudio em si.

A limitação do tier gratuito/básico de **512MB de RAM** do Render foi o gargalo. O processo sofria OOM (*Out of Memory*) kills constantes ao tentar carregar o modelo. Isso ilustra bem o dilema da "Edge AI" ou deploy em ambientes restritos: você pode ter o melhor modelo do mundo, mas se não tiver o hardware para servi-lo, ele não chega ao usuário final.

Apesar de que o nosso servidor executa a inferência do Basic Pitch, modelo de transcrição áudio para MIDI mais otimizado para CPU. Na época do hackathon lembro que tentei a inferência de outros modelos muito melhores em termos de acurácia e focados na task de transcrição de piano, escolhi bem um deles e consegui colocá-lo em um cluster pago com GPU -- o que não é viável para demonstração gratuita. Como fallback deixei o Basic Pitch do Spotify no gatilho, instalado em minha máquina. No fim das contas, nenhum dos dois estão sendo executados no deploy inicial do projeto. Creio que algum dia as máquinas fiquem mais baratas e eu possa fuçar um pouquinho mais desse projeto :).

### Teste Você Mesmo (Versão Demo)

Devido a essa limitação de infraestrutura atual, preparei uma versão de demonstração para vocês experimentarem a interface e a mecânica do jogo.

Você pode acessar agora em: **[hero-keys.vercel.app](https://hero-keys.vercel.app/)**

> **Nota:** Ao acessar, procure pela opção de **Demonstração (Mocked)** que é um botãozinho redondo bem discreto. Nessa versão, os arquivos MIDI já estão pré-processados para garantir que você tenha a experiência fluida da interface sem depender do backend de inferência pesada que ainda estou otimizando.

### Próximos Passos

O HeroKeys é um projeto *open-source* (licença MIT) e continuo trabalhando nele. Meus próximos objetivos são:

1.  **Otimização de Modelo:** Quantizar o modelo de transcrição ou migrar para uma solução *client-side* (TensorFlow.js ou ONNX), rodando a inferência direto no navegador do usuário para evitar custos de servidor.
2.  **Segmentação Automática:** Usar IA para detectar refrão, verso e introdução, permitindo loops de prática.
3.  **Melhorar o sistema de pontuação** Melhorar os scripts de score, sensibilidade de toque e múltiplos de pontos.

Se você curte música, código e IA, sinta-se à vontade para contribuir no repositório ou mandar um pull request!

---

*Gostou do projeto? Acompanhe meu blog para mais conteúdos sobre a intersecção entre música e inteligência artificial.*