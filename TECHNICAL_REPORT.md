# RELATÓRIO TÉCNICO
## Ferramenta Inteligente para Comunicação Proativa com o Segurado

**Projeto**: Desafio 5 — I2A2  
**Equipe**: Insurminds  
**Data**: Setembro de 2026  
**Versão**: 1.0.0 (MVP / Proof of Concept)  
**Posicionamento**: *"From Reactive Insurance to Proactive Protection"*

---

## 1. Resumo Executivo

O presente projeto apresenta o desenvolvimento e validação do MVP **Insurminds**, uma solução que transforma dados meteorológicos antecipados em ações preventivas personalizadas para segurados de carteiras Auto e Residencial. 

Ao romper com o paradigma estritamente reativo do setor segurador — que historicamente só interage com o cliente após o sinistro —, o sistema utiliza uma **arquitetura orientada a agentes desacoplada**, um **motor determinístico de regras de negócio** e **guardrails estritos de IA Generativa**. Os testes demonstraram 100% de conformidade nas decisões críticas, eliminação de alertas desnecessários e geração de recomendações claras e orientativas em múltiplos canais de comunicação.

---

## 2. Contexto e Problema

Seguradoras enfrentam custos bilionários anuais decorrentes de sinistros climáticos que poderiam ter suas perdas reduzidas ou mitigadas se os segurados fossem avisados com algumas horas de antecedência.

Por outro lado, o envio indiscriminado de notificações climáticas gerais gera **fadiga de alertas** e desengajamento do usuário. Um alerta de chuva para um segurado que possui apenas seguro auto sem risco de enchente ou granizo é irrelevante; já uma previsão de granizo de 15 minutos em Brasília para quem possui um veículo estacionado na rua representa um risco crítico e eminente.

O desafio central consiste em **orquestrar dados climáticos externos, cruzar com a carteira de segurados, aplicar regras de negócio auditáveis e produzir uma comunicação humana, empática e segura via LLM**.

---

## 3. Objetivos

### Objetivo Principal
Demonstrar que agentes inteligentes podem utilizar dados meteorológicos externos para antecipar riscos e disparar comunicações personalizadas e preventivas antes da ocorrência de possíveis sinistros.

### Objetivos Secundários
1. Integrar API meteorológica em tempo real (Open-Meteo) com fallback e injeção de simulação.
2. Detectar eventos climáticos com thresholds configuráveis.
3. Correlacionar eventos espaciais com segurados e tipologia de apólice.
4. Aplicar motor de regras determinístico independente da IA.
5. Classificar o risco através do *Preventive Insurance Score* (0 a 100).
6. Gerar mensagens via LLM respeitando canal e persona.
7. Submeter as mensagens a guardrails automáticos de segurança e conformidade.
8. Simular o envio nos canais WhatsApp, Push, SMS e E-mail.
9. Manter trilha de auditoria completa (*Audit Trail*).
10. Disponibilizar dashboard executivo e operacional para demonstração instantânea.

---

## 4. Arquitetura da Solução

A solução foi estruturada em camadas modulares e orientadas a agentes com fronteiras de responsabilidade bem delimitadas:

```
[Fontes Meteorológicas Externas / Open-Meteo]
                  │
                  ▼
       [1. Weather Data Agent]
                  │
                  ▼
       [2. Event Detection Agent]
                  │
                  ▼
       [3. Risk Assessment Agent]
                  │
                  ▼
       [4. Business Rules Engine] ◄── [5. Customer Matching Agent]
                  │                                  ▲
                  ▼                                  │
      [6. Communication Agent]            [Base de Segurados]
         (LLM + Guardrails)
                  │
                  ▼
     [7. Notification Simulator]
                  │
                  ▼
      [8. Audit Trail & Dashboard]
```

O princípio fundamental é a **separação entre inteligência generativa e autoridade decisória**:
- A **decisão de comunicar** é determinística e auditável (Rules Engine).
- A **IA Generativa** atua exclusivamente na redação da mensagem sob rigorosos guardrails.

---

## 5. Descrição dos Agentes

| Agente | Responsabilidade Principal | Entradas | Saídas |
| :--- | :--- | :--- | :--- |
| **Weather Data Agent** | Coletar e normalizar medições meteorológicas externas | Coordenadas / Cidade | Objeto `WeatherRawData` normalizado |
| **Event Detection Agent** | Avaliar variáveis contra thresholds configuráveis | `WeatherRawData` | Lista de `DetectedEvent` |
| **Risk Assessment Agent** | Calcular o *Preventive Insurance Score* | Eventos e variáveis | `RiskAssessment` (Score 0–100, Nível) |
| **Customer Matching Agent**| Cruzar localização do evento com carteira de apólices | Coordenadas, Raio | Lista de segurados na zona de risco |
| **Business Rules Engine** | Aplicar regras determinísticas de elegibilidade | Segurado + Clima + Risco | Decisão binária + Ação preventiva |
| **Communication Agent** | Redigir comunicação humana e contextualizada via LLM | Dados pré-IA estruturados | Mensagem personalizada |
| **Guardrails Service** | Interceptar e auditar a mensagem gerada | Mensagem bruta + Contexto | Validação de conformidade |
| **Notification Simulator** | Simular a entrega no canal preferencial | Notificação validada | Renderização no canal alvo |

---

## 6. Tecnologias Utilizadas

- **Backend**: Python 3.11+, FastAPI (assíncrono e tipado com Pydantic v2).
- **Servidor Web**: Uvicorn.
- **Frontend / Dashboard**: HTML5 semântico, CSS3 moderno (Glassmorphism, Dark Palette), Vanilla JavaScript puro e responsivo.
- **API Meteorológica**: Open-Meteo (API global em tempo real sem necessidade de chaves) com suporte configurável a OpenWeather.
- **Inteligência Artificial**: Google Gemini API (`google-generativeai`) com fallback dinâmico estruturado para resiliência contínua.
- **Banco de Dados & Persistência**: SQLite e estrutura de dados em memória para cache de auditoria.
- **Testes Automatizados**: Pytest.

---

## 7. Fontes de Dados

- **Open-Meteo Weather API**: Fornece previsões horárias, precipitação acumulada (mm), rajadas de vento (km/h), temperatura e códigos WMO de tempestade/granizo.
- **Base de Segurados Simulada (`data/customers.csv`)**: Registros completos contendo ID, Nome, Cidade, Estado, Latitude, Longitude, Tipo de Seguro (AUTO, HOME), Bem Segurado, Canal Preferencial (WHATSAPP, PUSH, SMS, EMAIL) e Perfil de Risco.

---

## 8. Estrutura dos Dados

### Customer
```json
{
  "id": "C001",
  "name": "João Porto",
  "city": "Brasília",
  "latitude": -15.7938,
  "longitude": -47.8827,
  "insurance_type": "AUTO",
  "asset": "SUV Honda HR-V",
  "preferred_channel": "WHATSAPP",
  "risk_profile": "MODERATE"
}
```

### Weather Event
```json
{
  "event_id": "EV-HAIL-9A1F2B",
  "event_type": "HAIL",
  "description": "Previsão de Granizo",
  "intensity_value": 1.0,
  "unit": "flag",
  "location": "Brasília",
  "timestamp": "2026-09-13T15:00:00"
}
```

### Notification Payload
```json
{
  "notification_id": "NOTIF-4B9F10A2",
  "customer_id": "C001",
  "channel": "WHATSAPP",
  "risk_level": "CRITICAL",
  "risk_score": 85,
  "message": "Olá, João. Há previsão de granizo para sua região nas próximas horas. Se possível, evite estacionar o veículo em áreas abertas e procure um local protegido.",
  "status": "SIMULATED_SENT"
}
```

---

## 9. Regras de Negócio

Implementadas de maneira puramente determinística em `app/rules/business_rules.py`:

- **Regra 1**: `IF hail = true AND insurance_type = AUTO THEN notify = true`
- **Regra 2**: `IF rain_mm > 40 AND insurance_type = HOME THEN notify = true`
- **Regra 3**: `IF wind_kmh > 60 AND insurance_type IN (HOME, AUTO) THEN notify = true`
- **Regra 4**: `IF risk_level = LOW THEN notification = false`

### Matriz de Decisão Operacional
| Evento | Seguro Auto | Seguro Residencial | Nível de Severidade |
| :--- | :---: | :---: | :---: |
| **Granizo** | **SIM** | **SIM** | Crítica |
| **Tempestade Severa** | **SIM** | **SIM** | Crítica |
| **Chuva Intensa (>40mm)** | **SIM** | **SIM** | Alta |
| **Vento Forte (>60km/h)** | **SIM** | **SIM** | Alta |
| **Chuva Leve (<20mm)** | **NÃO** | **NÃO** | Baixa (Suprimida) |

---

## 10. Sistema de Avaliação de Risco (Preventive Insurance Score)

O índice numérico (0 a 100) pondera o impacto das variáveis climáticas sobre o patrimônio do segurado:
- **0 a 29 (Baixo)**: Chuvas leves e ventos brandos. Não gera comunicação externa.
- **30 a 59 (Moderado)**: Chuvas moderadas (20–40 mm). Gera alerta informativo ou monitoramento preventivo.
- **60 a 79 (Alto)**: Chuvas intensas (40–60 mm) ou ventos superiores a 60 km/h. Disparo de notificação prioritária.
- **80 a 100 (Crítico)**: Granizo, tempestades severas ou precipitações extremas (>60 mm). Disparo emergencial imediato.

---

## 11. Utilização da Inteligência Artificial

A Inteligência Artificial Generativa é empregada para transformar parâmetros técnicos brutos em uma mensagem empática, clara e ajustada ao meio de comunicação do segurado. 

Ela recebe o contexto do segurado, do bem e da recomendação preventiva formulada pela regra de negócio, sintetizando o conteúdo de acordo com o canal:
- **WhatsApp**: Texto próximo, conciso, com espaçamento adequado.
- **Push Notification**: Direto e objetivo, com limite de até 120 caracteres.
- **SMS**: Notificação curta e informativa.
- **E-mail**: Formato consultivo, com saudação formal e assinatura institucional da seguradora.

---

## 12. Guardrails da IA

Para garantir total conformidade jurídica e de atendimento ao cliente, o serviço `app/services/guardrails.py` valida 100% das mensagens geradas antes de sua exibição:

1. **Vedação de Promessa de Cobertura**: Bloqueia termos como *"cobertura garantida"*, *"sua apólice cobre"*, *"reembolso assegurado"*.
2. **Vedação de Certeza de Dano**: Proíbe afirmações como *"seu carro certamente será danificado"*, mantendo sempre o tom probabilístico e preventivo.
3. **Vedação de Linguagem Alarmista**: Rejeita expressões de pânico (*"catástrofe iminente"*, *"corra para salvar sua vida"*).
4. **Vedação de Interpretação Contratual**: Bloqueia menção a cláusulas, condições gerais ou termos securitários complexos.
5. **Obrigatoriedade de Orientação Preventiva**: Exige verbos práticos de ação preventiva (*"recomenda"*, *"verificar"*, *"proteger"*, *"abrigar"*).

---

## 13. Fluxo End-to-End

1. O operador ou agendamento dispara a checagem meteorológica para uma cidade.
2. O **Weather Agent** obtém e normaliza os dados da API Open-Meteo.
3. O **Event Agent** classifica eventos conforme thresholds de chuva, vento e granizo.
4. O **Risk Agent** calcula o *Preventive Insurance Score* e determina a severidade.
5. O **Customer Matching Agent** filtra clientes no raio geográfico do evento.
6. O **Business Rules Engine** avalia a elegibilidade determinística de cada cliente.
7. O **Communication Agent** gera as mensagens via LLM para os elegíveis.
8. Os **Guardrails** auditam e chancelam cada mensagem.
9. O **Notification Simulator** simula a entrega visual no canal preferencial.
10. O **Audit Service** grava o histórico detalhado em banco de dados e memória.
11. O **Dashboard** atualiza os KPIs, tabelas e visualização no smartphone em tempo real.

---

## 14. Interface do Sistema

O dashboard web construído oferece:
- **Painel de Cenários Instantâneos**: Botões de 1 clique para reproduzir os Cenários A, B, C e D.
- **Simulador Interativo Personalizado**: Sliders e checkboxes para testar qualquer combinação meteorológica customizada.
- **Pipeline Animado dos Agentes**: Indicação visual do fluxo de dados percorrendo cada nó do sistema.
- **Tabela de Eventos (Tela 17 do PRD)**: Monitoramento de intensidade, risco e status.
- **Tabela de Segurados (Tela 18 do PRD)**: Filtro da carteira de apólices e canais.
- **Comparador Antes da IA vs Depois da IA (Tela 19 do PRD)**: Inspeção lado a lado dos dados técnicos brutos contra o texto refinado gerado pela IA.
- **Simulador de Smartphone (Seção 15 do PRD)**: Dispositivo realista simulando a tela de mensagens com layout oficial.
- **Audit Trail (Tela 20 do PRD)**: Linha do tempo com carimbo de microssegundos e rastreabilidade total.

---

## 15. Exemplos de Mensagens

### Exemplo 1: Granizo + Auto (WhatsApp)
> *"Olá, João. Há previsão de granizo para sua região nas próximas horas. Se possível, evite estacionar o veículo em áreas abertas e procure um local protegido."*

### Exemplo 2: Chuva Intensa + Residencial (WhatsApp)
> *"Olá, Maria. Há previsão de chuva intensa para sua região nas próximas horas. Como medida preventiva, recomendamos verificar calhas, ralos e áreas externas da residência e retirar objetos que possam ser deslocados pela água ou pelo vento."*

### Exemplo 3: Vento Forte + Residencial (E-mail)
> *"Prezado(a) Carlos Eduardo, identificamos a previsão de ventos fortes para sua região em Florianópolis nas próximas horas. Pensando na proteção do seu Apartamento Beira-Mar, recomendamos verificar objetos soltos em áreas externas, portas, janelas e estruturas leves. Equipe de Proteção Preventiva — Insurminds Seguros."*

---

## 16. Casos de Demonstração Obrigatórios

- **Cenário A (Granizo + Auto em Brasília)**: Dispara notificação crítica para o segurado João Porto via WhatsApp.
- **Cenário B (Chuva Intensa > 40mm + Residencial em São Paulo)**: Dispara notificação de alto risco para calhas e ralos.
- **Cenário C (Vento Forte > 60km/h + Residencial em Florianópolis)**: Dispara notificação de alto risco para janelas e estruturas externas.
- **Cenário D (Chuva Leve / Evento Irrelevante em Curitiba)**: O sistema calcula score de risco baixo (< 30) e a Regra 4 suprime o disparo, comprovando a proteção ativa contra spam.

---

## 17. Resultados e KPIs do MVP

- **Taxa de Conformidade dos Guardrails**: 100% de aprovação (zero violações contratuais ou alarmistas).
- **Tempo Médio de Processamento End-to-End**: ~45 ms (com template) e ~800 ms (com LLM externa).
- **Eficiência de Filtragem de Ruído**: 100% de supressão no Cenário D.
- **Cobertura de Testes Automatizados**: 11 testes unitários passando com 100% de sucesso.

---

## 18. Limitações Conhecidas do MVP

- O envio para os canais (WhatsApp, Push, SMS, E-mail) é executado em modo de simulação visual de alta fidelidade, não se conectando a brokers de telecomunicação reais (Twilio, Z-API, Firebase Cloud Messaging).
- O matching geográfico baseia-se em cidades e coordenadas centroidais, sem polígonos geoespaciais complexos de microbacias hidrográficas.
- O cálculo de risco utiliza matriz heurística e thresholds parametrizáveis, não incorporando modelos preditivos supervisionados de machine learning treinados em bases históricas reais de sinistros.

---

## 19. Evolução Futura (Roadmap)

- **Fase 2 (Canais Reais)**: Integração com WhatsApp Business API (Meta), Firebase Cloud Messaging (Push) e provedores de SMS.
- **Fase 3 (Machine Learning Preditivo)**: Treinamento de modelos supervisionados com dados históricos de sinistralidade (precipitação acumulada vs probabilidade estatística de alagamento por CEP).
- **Fase 4 (Ecossistema Preventivo)**: Gamificação e bonificação de apólices com descontos na renovação para clientes que confirmam ações preventivas no aplicativo da seguradora.
