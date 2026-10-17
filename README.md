# 🧠 Guía rápida: Hindsight — memoria que aprende para tus agentes (ES · PT)

Una sola página para usar **Hindsight** (de Vectorize): qué es, las tres operaciones que hay que entender (retain, recall, reflect), cómo se instala y en qué se diferencia de guardar la conversación en una base vectorial.

**Autor:** Guangyi Zhao · Octubre 2026

Proyecto original: https://github.com/vectorize-io/hindsight (50.746 estrellas, consultado el 17 de octubre de 2026)

> **Nota de autoría:** esta guía es una síntesis original, redactada por el autor a partir de la documentación pública del proyecto y de su propio uso. No es una traducción ni una copia de la documentación oficial, y no es un proyecto oficial de Vectorize. Para el detalle completo, ve al repositorio enlazado.

---

## 🇪🇸 Español

### Qué es

Hindsight es un sistema de memoria para agentes que busca que el agente **aprenda**, no que solo recuerde. La mayoría de las «memorias» guardan el historial de conversación y lo recuperan por similitud; Hindsight organiza lo que entra en estructuras parecidas a la memoria humana y consolida creencias con evidencia. Es el sistema con mejor resultado publicado en el benchmark LongMemEval (resultados reproducidos de forma independiente, según el proyecto), y se usa en producción en empresas grandes y startups.

### Los tipos de memoria

| Tipo | Qué guarda | Ejemplo |
|---|---|---|
| **Hechos del mundo** (world facts) | Verdades generales | «La estufa quema» |
| **Experiencias** (experiences) | Lo que le pasó al propio agente | «Toqué la estufa y dolió» |
| **Observaciones** (observations) | Creencias consolidadas a partir de muchas memorias, con su evidencia y contador de pruebas; se refinan con nueva evidencia en vez de sobrescribirse | «Este cliente prefiere respuestas cortas» |
| **Modelos mentales** (mental models) | La respuesta permanente a una pregunta («¿cuáles son las preferencias de este usuario?»): la defines una vez y el sistema la reescribe en segundo plano a medida que aprende; leerla es una lectura de base de datos, sin llamada al LLM | La página de conocimiento con la que el agente arranca cada sesión |

Todo vive en **bancos** (banks): almacenes aislados, uno por usuario, agente o proyecto, sin fugas entre bancos. Cada banco puede tener rasgos de disposición (escepticismo, literalidad, empatía) que moldean cómo razona el sistema sobre sus memorias.

### Las tres operaciones

| Operación | Qué hace | Cuándo usarla |
|---|---|---|
| **retain** | Guarda información nueva; un LLM extrae hechos, entidades, relaciones y datos temporales y los normaliza en índices buscables | Al final de cada conversación o evento importante |
| **recall** | Recupera memorias combinando 4 estrategias en paralelo: semántica (vectores), palabras clave (BM25), grafo (entidades/tiempo/causa) y temporal; fusiona resultados y los reordena con un cross-encoder | Antes de responder, para traer el contexto relevante |
| **reflect** | Analiza las memorias en profundidad para formar conexiones y responder preguntas que piden juicio, no búsqueda | Riesgos de un proyecto, por qué algo funcionó, patrones de un cliente |

### Instalación

- **Docker (recomendado):** la imagen oficial levanta la API en el puerto 8888 y la interfaz web en el 9999, con su base de datos integrada.
- **pip:** `pip install hindsight-api` y a correr el servidor en local.
- **Kubernetes:** chart de Helm oficial.
- **Cloud:** también existe el servicio alojado de Vectorize, si no quieres operar nada.

Funciona con más de 25 proveedores LLM (OpenAI, Anthropic, Gemini, Groq, Bedrock, Vertex AI, DeepSeek y otros), con modelos totalmente locales (Ollama, LM Studio, llama.cpp) y con suscripciones existentes (ChatGPT, Claude, Cursor, Copilot) sin clave API adicional: se elige con la variable del proveedor al arrancar.

### Integrarlo en tu agente

- **Wrapper de dos líneas:** envuelves tu cliente LLM actual y Hindsight recupera memorias antes de cada llamada y guarda la conversación después, automáticamente.
- **Servidor MCP:** para agentes compatibles con MCP.
- **Skill de documentación:** para Claude Code, Cursor y otros agentes de código, `npx skills add https://github.com/vectorize-io/hindsight --skill hindsight-docs`.

### Dos extras que importan

- **Defensa de memoria (Memory Defense):** política opcional por banco que escanea cada `retain` contra 45 patrones de secretos y datos personales (PII): redacta la coincidencia o bloquea el ítem antes de que se almacene.
- **Multilingüe por defecto:** el idioma de entrada se detecta y se conserva de extremo a extremo; los hechos quedan en su idioma original y las entidades en su escritura nativa.

### Hindsight frente a «vectores y ya»

| | Base vectorial simple | Hindsight |
|---|---|---|
| Qué guarda | Fragmentos de texto | Hechos, experiencias, observaciones y modelos mentales |
| Qué devuelve | Los fragmentos más parecidos | Respuestas consolidadas con evidencia, o el modelo mental ya redactado |
| El tiempo | Un campo más | Estrategia temporal propia en la recuperación |
| Aprender | Nada cambia entre sesiones | Las observaciones y los modelos mentales se refinan en segundo plano |

### ⚠️ Errores típicos

- **Usarlo como una base vectorial más.** Si solo haces retain/recall por similitud, pierdes justo lo que lo hace distinto: observaciones y reflect.
- **Un solo banco para todos los usuarios.** Los bancos existen para aislar: un banco por usuario, agente o proyecto.
- **No definir modelos mentales.** Sin la pregunta permanente, cada sesión vuelve a «redescubrir» lo que el agente ya sabía.

---

## 🇵🇹 Português

### O que é

O Hindsight é um sistema de memória para agentes que busca que o agente **aprenda**, não apenas lembre. A maioria das «memórias» guarda o histórico da conversa e o recupera por similaridade; o Hindsight organiza o que entra em estruturas parecidas com a memória humana e consolida crenças com evidência. É o sistema com o melhor resultado publicado no benchmark LongMemEval (resultados reproduzidos de forma independente, segundo o projeto), e é usado em produção em empresas grandes e startups.

### Os tipos de memória

| Tipo | O que guarda | Exemplo |
|---|---|---|
| **Fatos do mundo** (world facts) | Verdades gerais | «O fogão queima» |
| **Experiências** (experiences) | O que aconteceu com o próprio agente | «Toquei no fogão e doeu» |
| **Observações** (observations) | Crenças consolidadas a partir de muitas memórias, com sua evidência e contador de provas; refinam-se com nova evidência em vez de serem sobrescritas | «Este cliente prefere respostas curtas» |
| **Modelos mentais** (mental models) | A resposta permanente a uma pergunta («quais são as preferências deste usuário?»): você a define uma vez e o sistema a reescreve em segundo plano conforme aprende; lê-la é uma leitura de banco de dados, sem chamada ao LLM | A página de conhecimento com que o agente inicia cada sessão |

Tudo vive em **bancos** (banks): armazéns isolados, um por usuário, agente ou projeto, sem vazamento entre bancos. Cada banco pode ter traços de disposição (ceticismo, literalidade, empatia) que moldam como o sistema raciocina sobre suas memórias.

### As três operações

| Operação | O que faz | Quando usar |
|---|---|---|
| **retain** | Guarda informação nova; um LLM extrai fatos, entidades, relações e dados temporais e os normaliza em índices buscáveis | Ao fim de cada conversa ou evento importante |
| **recall** | Recupera memórias combinando 4 estratégias em paralelo: semântica (vetores), palavras-chave (BM25), grafo (entidades/tempo/causa) e temporal; funde os resultados e os reordena com um cross-encoder | Antes de responder, para trazer o contexto relevante |
| **reflect** | Analisa as memórias em profundidade para formar conexões e responder perguntas que pedem julgamento, não busca | Riscos de um projeto, por que algo funcionou, padrões de um cliente |

### Instalação

- **Docker (recomendado):** a imagem oficial sobe a API na porta 8888 e a interface web na 9999, com seu banco de dados integrado.
- **pip:** `pip install hindsight-api` e rode o servidor localmente.
- **Kubernetes:** chart Helm oficial.
- **Cloud:** também existe o serviço hospedado da Vectorize, se você não quer operar nada.

Funciona com mais de 25 provedores LLM (OpenAI, Anthropic, Gemini, Groq, Bedrock, Vertex AI, DeepSeek e outros), com modelos totalmente locais (Ollama, LM Studio, llama.cpp) e com assinaturas existentes (ChatGPT, Claude, Cursor, Copilot) sem chave API adicional: escolhe-se com a variável do provedor ao iniciar.

### Integrar ao seu agente

- **Wrapper de duas linhas:** você envolve o seu cliente LLM atual e o Hindsight recupera memórias antes de cada chamada e guarda a conversa depois, automaticamente.
- **Servidor MCP:** para agentes compatíveis com MCP.
- **Skill de documentação:** para Claude Code, Cursor e outros agentes de código, `npx skills add https://github.com/vectorize-io/hindsight --skill hindsight-docs`.

### Dois extras que importam

- **Defesa de memória (Memory Defense):** política opcional por banco que escaneia cada `retain` contra 45 padrões de segredos e dados pessoais (PII): redige a correspondência ou bloqueia o item antes de ser armazenado.
- **Multilíngue por padrão:** o idioma de entrada é detectado e preservado de ponta a ponta; os fatos ficam no idioma original e as entidades na sua escrita nativa.

### Hindsight frente a «vetores e pronto»

| | Base vetorial simples | Hindsight |
|---|---|---|
| O que guarda | Fragmentos de texto | Fatos, experiências, observações e modelos mentais |
| O que devolve | Os fragmentos mais parecidos | Respostas consolidadas com evidência, ou o modelo mental já redigido |
| O tempo | Um campo a mais | Estratégia temporal própria na recuperação |
| Aprender | Nada muda entre sessões | As observações e os modelos mentais se refinam em segundo plano |

### ⚠️ Erros típicos

- **Usá-lo como uma base vetorial comum.** Se você só faz retain/recall por similaridade, perde justamente o que o torna diferente: observações e reflect.
- **Um único banco para todos os usuários.** Os bancos existem para isolar: um banco por usuário, agente ou projeto.
- **Não definir modelos mentais.** Sem a pergunta permanente, cada sessão volta a «redescobrir» o que o agente já sabia.

---

## 🇬🇧 English summary

One-page cheat sheet (Spanish + Portuguese) for **Hindsight** (Vectorize): the agent memory system built so agents learn, not just remember — top published score on the LongMemEval benchmark. Covers the four memory types (world facts, experiences, observations refined with evidence instead of overwritten, and mental models: standing answers rewritten in the background and readable without an LLM call), memory banks as strictly isolated stores with disposition traits, and the three operations — retain (LLM-extracted facts and entities), recall (semantic + BM25 + graph + temporal retrieval fused and reranked), reflect (disposition-aware reasoning over memories). Also the install routes (Docker with API on 8888 and UI on 9999, pip, Helm, or the hosted cloud), 25+ LLM providers including local models, the two-line LLM wrapper and MCP integration, Memory Defense secret/PII scanning, and the typical mistakes: treating it as a plain vector store, sharing one bank across users, and never defining mental models. Original synthesis by the author from the project's public documentation; not an official Vectorize document and not a translation.

**Author:** Guangyi Zhao · October 2026 · MIT License
