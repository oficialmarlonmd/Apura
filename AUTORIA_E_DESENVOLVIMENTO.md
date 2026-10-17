# Documentação de Autoria e Desenvolvimento Técnico

**Projeto:** Apura / verificAI  
**Responsável Técnico Principal (Backend & Machine Learning):** Marlon Marques ([@oficialmarlonmd](https://github.com/oficialmarlonmd))  
**Repositório Oficial Pessoal:** [https://github.com/oficialmarlonmd/Apura](https://github.com/oficialmarlonmd/Apura)  
**Modelo Hugging Face:** [`oficialmarlon/bertimbau-fakenews-detector-v4`](https://huggingface.co/oficialmarlon/bertimbau-fakenews-detector-v4)

---

## 1. Visão Geral das Responsabilidades

Neste projeto de combate à desinformação em língua portuguesa, **Marlon Marques** atuou como o **engenheiro principal de Backend e Machine Learning**, sendo responsável por conceber, estruturar e implementar todo o núcleo analítico e preditivo do sistema, desde a integração de modelos de Deep Learning até a arquitetura de microsserviços e governança de risco:

1. **Arquitetura e Desenvolvimento do Backend (Django + Django REST Framework):**
   - Criação da estrutura base do projeto Django (`apura`), endpoints RESTful versionados (`/api/v1/analyses/`), validação rigorosa de schemas e orquestração assíncrona.
   - Configuração de ambiente de produção, variáveis de ambiente, Dockerfile e Docker Compose.

2. **Modelo de Machine Learning — BERTimbau V4:**
   - Adaptação, fine-tuning e empacotamento do modelo **BERTimbau** (`neuralmind/bert-base-portuguese-cased`) para classificação de desinformação em língua portuguesa.
   - Publicação e versionamento no Hugging Face Hub: [`oficialmarlon/bertimbau-fakenews-detector-v4`](https://huggingface.co/oficialmarlon/bertimbau-fakenews-detector-v4).
   - Gerenciamento de pesos binários (`model.safetensors` de ~436 MB) via **Git LFS** com mecanismo de fallback inteligente (download automático via Hugging Face Hub se clonado sem LFS).
   - Aceleração por hardware adaptativa (suporte a CUDA em Linux/Windows e Metal Performance Shaders - MPS no macOS Apple Silicon).

3. **Lógica de Fatiamento Determinístico (*Chunking*) e Tokens Sintáticos (`[SEP]`):**
   - Implementação de representação estruturada de matérias jornalísticas:
     $$\text{[CLS] título [SEP] subtítulo [SEP] trecho\_do\_corpo [SEP]}$$
   - Algoritmo de fatiamento com sobreposição (*stride*) alinhado a fronteiras de palavras inteiras (impedindo quebras artificiais de sub-palavras `##`).
   - Inferência heurística de títulos para textos não estruturados e expansão da capacidade de análise para até 20.000 caracteres.

4. **Motor de Governança de Risco Estatístico e Abstenção (RF14):**
   - Criação de uma fórmula estritamente probabilística para o Índice de Risco (0 a 100), combinando sinais do modelo BERTimbau (60%), indicadores discursivo-linguísticos (15%) e conflito com fontes externas de checagem (25%), com normalização dinâmica em caso de sinais ausentes.
   - Implementação da **Regra de Abstenção Estatística (RF14)**: Quando a confiança do modelo é inferior a 70% ou o desacordo entre trechos (*chunk disagreement*) excede 35%, o sistema se abstém de emitir notas arbitrárias e declara a análise como `inconclusiva`.

5. **Pipeline de Extração Web & Segurança (SSRF Protection):**
   - Implementação da análise por link com extração de artigos via `trafilatura`.
   - Camadas de segurança contra ataques de SSRF (Server-Side Request Forgery) em redirecionamentos e resoluções DNS para redes privadas/locais.

6. **Integração Multi-Fontes de Fact-Checking & Fallbacks:**
   - Integração com a Google Fact Check Tools API para consulta de alegações verificadas.
   - Desenvolvimento de fallback inteligente multi-fontes consultando **Projeto Comprova**, **Boatos.org** e **Wikipédia**.
   - Comparação semântica e contextual entre a alegação da matéria e o consenso das fontes verificadas.

7. **Salvaguardas Epistemológicas e Prevenção de Alucinação (LLM):**
   - Modulação e calibração de prompts de IA generativa para evitar sobreposição indevida do LLM sobre os sinais empíricos do BERTimbau.
   - Detecção especializada de **Sátira e Paródia Jornalística** (ex: Sensacionalista), isolando alertas metodológicos de escopo.
   - Alertas automáticos de discrepância entre estilo formal de escrita e conteúdo factualmente falso.

8. **Integração Frontend e Visualização de Métricas:**
   - Integração da API com o frontend **ApuraWeb** (React 19 + Vite 8).
   - Exibição de probabilidades calculadas pelo modelo, painel técnico de métricas estatísticas e alinhamento com padrões de acessibilidade **WCAG 2.2 AA**.

---

## 2. Histórico de Commits e Contribuições no Repositório

Principais commits concebidos e implementados por **Marlon Marques (`@oficialmarlonmd`)**:

| Commit | Mensagem / Escopo Técnico |
|---|---|
| `f2bb56c` | **Initial commit**: Criação e inicialização do projeto. |
| `5bac241` | **Enhance README**: Estruturação arquitetural do projeto e documentação inicial de setup. |
| `18049e1` | **feat: setup django backend com BERTimbau V4 e Git LFS**: Implementação do backend Django, arquitetura do classificador BERTimbau V4, Git LFS, tokenizer, pesos e endpoint inicial de predição. |
| `251f63c` | **feat: integrar frontend web (React 19 + Vite 8) do apuraWeb**: Conexão entre o backend Django REST Framework e a interface React moderna. |
| `ead92c6` | **feat: implementar analise por link, raspagem web com trafilatura, google fact check e sintese explicativa por llm**: Pipeline completo de raspagem, checagem cruzada e síntese via IA. |
| `ae82e73` | **feat: expandir limite maximo de texto de 5.000 para 20.000 caracteres no backend e frontend**: Fatiamento escalável com preservação de prefixo e agregação por janelas. |
| `3ec2a7b` | **feat(ui): exibir probabilidade calculada pelo modelo BERTimbau na tela de resultado**: Exposição transparente dos dados inferidos pelo modelo e painel técnico. |
| `cadba7a` | **feat: comparacao semantica com consenso, analise discursiva e alertas de incerteza**: Análise do discurso, semântica de alegações e sinalizadores de confiabilidade. |
| `a8e8db1` | **fix: harmonizar calculo de risco com divergencia de consenso e adicionar alerta de discrepancia estilo vs fato**: Resolução de conflitos entre redação formal e desinformação fática. |
| `b54b16b` | **feat: priorizar fact-checking e sinalizar escopo de treino do modelo**: Ponderação de evidências externas sobre heurísticas estilísticas. |
| `b7c7de3` | **fix: calibrar analise para imprensa tradicional e isolar alertas de conspiracao**: Ajuste fino de sensibilidade para evitar falsos positivos em veículos tradicionais de imprensa. |
| `346dcbe` | **feat: detectar satira e parodia jornalistica com alerta metodologico de escopo**: Classificador heurístico e contextual para portais de humor e sátira. |
| `d409b65` | **feat: integrar fallback multi-fontes de checagem com Comprova, Boatos.org e Wikipedia**: Resiliência na busca por checagens em múltiplos agregadores brasileiros. |
| `dda02ac` | **fix: calibrar salvaguarda epistemológica para notícias falsas com redação formal e evitar sobreposição indevida do LLM**: Proteção contra viés de autoridade do LLM diante de fake news sofisticadas. |

*(Nota: O projeto contou com contribuições colaborativas em etapas de extensão e ajustes de validação de frontend).*

---

## 3. Detalhes de Engenharia do Modelo BERTimbau V4

### 3.1. Treinamento e Hiperparâmetros
- **Base Pré-treinada:** `neuralmind/bert-base-portuguese-cased` (110 milhões de parâmetros).
- **Tarefa:** Classificação binária de sequências textuais (0 = Confiável / Legítima, 1 = Desinformação / Falsa).
- **Entrada Estruturada:** `[CLS] {titulo} [SEP] {subtitulo} [SEP] {corpo} [SEP]`.
- **Janela de Contexto:** `MAX_LEN = 128` tokens por janela, com preservação de até 50% de prefixo (título + subtítulo) em todas as fatias.

### 3.2. Fatiamento Determinístico
```text
Texto Longo (até 20.000 caracteres)
       │
       ▼
Detecção de Título/Subtítulo (Preservação de Prefixo)
       │
       ▼
Fatiamento do Corpo em Janelas com Stride (Sobreposição de 32 tokens)
       │
       ▼
Alinhamento com Fronteiras de Palavras (sem sub-tokens '##' no início de janelas)
       │
       ▼
Inferência Paralela / Batch das Janelas no BERTimbau V4
       │
       ▼
Agregação Ponderada (Média P(falsa), Confiança, e Desvio-Padrão / Desacordo)
```

---

## 4. Contato e Referências

- **Engenheiro:** Marlon Marques
- **E-mail:** [marlon.marques.mmd@gmail.com](mailto:marlon.marques.mmd@gmail.com)
- **GitHub:** [@oficialmarlonmd](https://github.com/oficialmarlonmd)
- **Repositório do Projeto:** [github.com/oficialmarlonmd/Apura](https://github.com/oficialmarlonmd/Apura)
- **Hugging Face:** [huggingface.co/oficialmarlon](https://huggingface.co/oficialmarlon)
