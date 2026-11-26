# 📋 Backlog do Produto, Planejamento de Sprints e Quadro Kanban

**Projeto 16:** Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo  
**Metodologia Ágil:** Scrumban (Ciclos de Sprints alinhados aos Marcos M1–M4 + Fluxo Contínuo em Quadro Kanban)  
**Escala de Pontuação:** Story Points em Fibonacci (`1`, `2`, `3`, `5`, `8`, `13`)

---

## 1. Estrutura do Quadro Kanban (GitHub Projects / Trello)

O quadro de gestão de tarefas da equipe é organizado nas seguintes colunas operacionais e limites de WIP (*Work In Progress*):

| Coluna | Descrição | Critério de Entrada / Saída |
| :--- | :--- | :--- |
| **1. Product Backlog** | Todas as Histórias de Usuário priorizadas pelo valor de negócio (LBI / segurança do aluno). | Histórias escritas com Persona, Critérios de Aceite e Story Points. |
| **2. Sprint Backlog (To Do)** | Histórias e tarefas técnicas selecionadas para o Marco/Sprint atual. | Comprometimento da equipe na Sprint Planning. |
| **3. Em Progresso (Doing)** | Tarefas em desenvolvimento ativo (limite recomendado: máx. 2 por integrante). | Branch `feature/USxx-...` criada a partir da `develop`. |
| **4. Code Review / QA** | Pull Request aberto aguardando revisão de outro membro e validação dos critérios de aceite. | PR preenchido, sem conflitos e validado localmente. |
| **5. Concluído (Done)** | Funcionalidade aprovada e mesclada na branch `develop` (e `main` no fechamento do Marco). | Atende 100% à *Definition of Done* do `CONTRIBUTING.md`. |

### Etiquetas (Labels) Padronizadas
- **Camada:** `frontend`, `backend`, `database`, `docs`, `ux-wireframe`
- **Prioridade:** `P0-critico`, `P1-alta`, `P2-media`
- **Marco:** `M1-Setup`, `M2-Desenvolvimento-I`, `M3-Desenvolvimento-II`, `M4-Polimento-Cloud`

---

## 2. Cronograma de Marcos e Sprints

| Marco / Sprint | Período | Entrega Oficial | Peso na Nota | Foco Principal |Capacidade Planejada |
| :--- | :--- | :---: | :---: | :--- | :---: |
| **Mês 01 (Marco M1)** | 11/11/26 a 02/12/26 | **02/12/2026** | **10%** | Imersão BRD/PRD, Arquitetura (ADR/C4), Modelagem v0.1, GitFlow, Backlog e Wireframes | **21 pts** |
| **Mês 02 (Marco M2)** | 03/12/26 a 13/01/27 | **13/01/2027** | **15%** | Setup Django/Next.js, Autenticação/RBAC, Cadastros Base (Alunos, Necessidades, Escolas, Vagas e Frota) | **34 pts** |
| **Mês 03 (Marco M3)** | 14/01/27 a 17/02/27 | **17/02/2027** | **15%** | Solicitações/Enrollment, Rotas Inteligentes (US02), Viagens, App do Monitor Check-in/Out (US01) | **39 pts** |
| **Mês 04 (Marco M4)** | 18/02/27 a 17/03/27 | **17/03/2027** | **60%** | Mapa em Tempo Real (Mãe/Gestor), Portal da Diretora, Notificações Push, Auditoria LGPD e Deploy Cloud | **34 pts** |

---

## 3. Épicos do Produto

- **ÉPICO 01 — Engenharia, Arquitetura e UX Inclusiva:** Setup de repositório, modelagem relacional/geoespacial, Design System acessível e prototipação das 5 personas.
- **ÉPICO 02 — Gestão Escolar, Inclusão e Solicitação de Vagas:** Cadastro de estudantes, necessidades de mobilidade (`StudentNeed`), responsáveis, escolas, vagas adaptadas e vínculos (`Enrollment`).
- **ÉPICO 03 — Gestão de Frota Adaptada e Roteirização Logística:** Cadastro de vans com elevador hidráulico, motoristas, monitores e cálculo de itinerários otimizados (`US02`).
- **ÉPICO 04 — Operação em Campo, Monitoramento em Tempo Real e Famílias:** Execução de viagens, check-in/check-out em 1 toque pela monitora (`US01`), GPS em tempo real, notificações aos pais e painel de chegada na escola.

---

## 4. Backlog Detalhado de Histórias de Usuário (User Stories)

### 🟢 Sprint / Marco 1 — Iniciação, Arquitetura & Prototipação (Entrega: 02/12/2026)

| ID | História de Usuário / Entregável Técnico | Persona / Ator | Story Points | Responsáveis | Status |
| :---: | :--- | :--- | :---: | :--- | :---: |
| **US00.1** | **Como** Equipe de Engenharia, **quero** estruturar o repositório GitHub com `README.md`, `CONTRIBUTING.md`, `LICENSE`, GitFlow (`main`/`develop`) e templates de PR/Issue, **para** garantir governança e colaboração padronizada. | Equipe de Desenvolvimento | **3** | Eduardo, Erick, Rita | ✅ Concluído |
| **US00.2** | **Como** Arquiteto de Dados, **quero** definir a modelagem conceitual/lógica (23 entidades em 6 domínios), dicionário de dados e regras de integridade/LGPD, **para** embasar a criação segura do banco PostgreSQL + PostGIS. | Banco de Dados & Modelagem | **5** | Edmilson, Karina, Eduardo | ✅ Concluído |
| **US00.3** | **Como** Arquiteto de Software, **quero** documentar as Decisões Arquiteturais (ADRs) e os diagramas C4 (Contexto, Contêineres, Módulos e Infraestrutura), **para** alinhar as escolhas de stack entre Frontend, Backend e Cloud. | Equipe Técnica / Orientador (PO) | **5** | Eduardo, Rita, Erick | ✅ Concluído |
| **US00.4** | **Como** Designer de Interface (UI/UX), **quero** elaborar as regras do Design System inclusivo e os Wireframes de baixa/média fidelidade das 5 personas, **para** validar o fluxo de telas antes da codificação. | Todas as 5 Personas | **8** | Eduardo, Erick | 🔄 Em Validação |

---

### 🔵 Sprint / Marco 2 — Desenvolvimento I: Fundação, Cadastros & Vagas (Entrega: 13/01/2027)

#### US03 — Autenticação Multi-Perfil e Isolamento por Organização
- **História:** *Como* Gestor da Prefeitura (`Organization`), *quero* que cada usuário (Gestor, Diretora, Motorista, Monitora e Responsável) acesse apenas as funcionalidades e dados permitidos ao seu perfil, *para* proteger dados sensíveis de crianças conforme a LGPD.
- **Persona:** Eng. Fernando ("Nando") e todos os perfis
- **Story Points:** `8` | **Prioridade:** `P0-critico` | **Responsáveis:** Rita, Erick (Back) / Eduardo (Front)
- **Critérios de Aceite:**
  - [ ] Implementação dos models `Organization`, `User` (customizado com UUID) e `OrganizationMembership`.
  - [ ] Login via JWT/Sessão segura redirecionando cada persona para sua interface correspondente (Dashboard Web ou App Mobile-First).
  - [ ] Bloqueio automático de acesso cruzado entre organizações distintas.

#### US04 — Cadastro de Aluno, Responsáveis e Necessidades Específicas de Deslocamento
- **História:** *Como* Gestor Escolar/Transporte, *quero* cadastrar os estudantes com seus responsáveis autorizados e suas necessidades específicas de mobilidade (tipo de cadeira de rodas, necessidade de acompanhante, auxílio no embarque e observações restritas), *para* garantir que apenas veículos compatíveis sejam alocados.
- **Persona:** Eng. Fernando ("Nando") e Solange ("Sol")
- **Story Points:** `8` | **Prioridade:** `P0-critico` | **Responsáveis:** Edmilson, Karina (DB) / Rita (Back) / Eduardo, Erick (Front)
- **Critérios de Aceite:**
  - [ ] CRUD de `Student`, `Guardian`, `StudentGuardian` (com flag `can_receive_notifications`) e `StudentNeed`.
  - [ ] Formulário no Frontend validado com `React Hook Form + Zod`.
  - [ ] Campo `mobility_notes` de `StudentNeed` visível apenas para perfis operacionais autorizados (conformidade LGPD).

#### US05 — Gestão de Escolas e Capacidade de Vagas Adaptadas
- **História:** *Como* Diretora Escolar ou Gestor, *quero* cadastrar as escolas com localização geográfica e definir a capacidade total e adaptada (`Vacancy`) por ano letivo, *para* controlar a oferta real de atendimento inclusivo.
- **Persona:** Profa. Tássia Camargo e Eng. Fernando
- **Story Points:** `5` | **Prioridade:** `P1-alta` | **Responsáveis:** Rita (Back) / Erick (Front)
- **Critérios de Aceite:**
  - [ ] Cadastro de `School` com endereço e coordenadas (`latitude/longitude` / PostGIS).
  - [ ] Cadastro de `Vacancy` com `total_capacity` e `accessible_capacity` validando que capacidade adaptada não exceda a total.

#### US06 — Cadastro de Frota Adaptada, Motoristas e Monitores
- **História:** *Como* Gestor da Frota, *quero* cadastrar os veículos informando placa, capacidade de passageiros, vagas de cadeira de rodas e presença de elevador hidráulico (`has_hydraulic_lift`), além de motoristas (com validade da CNH D) e monitores, *para* habilitar o planejamento seguro das viagens.
- **Persona:** Eng. Fernando ("Nando"), Seu Valdir e Marisa ("Mari")
- **Story Points:** `5` | **Prioridade:** `P1-alta` | **Responsáveis:** Rita, Erick (Back) / Eduardo (Front)
- **Critérios de Aceite:**
  - [ ] CRUD de `Vehicle`, `Driver` e `Monitor`.
  - [ ] Alerta/bloqueio caso a CNH do motorista esteja vencida (`cnh_expiration < hoje`).
  - [ ] Indicador visual claro nos veículos que possuem elevador hidráulico e vagas para cadeira de rodas.

#### US07 — Solicitação de Vaga e Aprovação de Vínculo de Transporte (`Enrollment`)
- **História:** *Como* Mãe/Responsável ou Secretaria Escolar, *quero* solicitar uma vaga de transporte adaptado informando o endereço de embarque do aluno e acompanhar a aprovação (`PENDING` → `APPROVED`), *para* gerar o vínculo ativo (`Enrollment`) que autoriza o transporte.
- **Persona:** Solange ("Sol") e Eng. Fernando
- **Story Points:** `8` | **Prioridade:** `P0-critico` | **Responsáveis:** Rita (Back) / Eduardo, Erick (Front)
- **Critérios de Aceite:**
  - [ ] Registro de `TransportRequest` com endereço e coordenadas de embarque.
  - [ ] Ao aprovar a solicitação, o backend cria automaticamente o registro em `Enrollment` e atualiza a ocupação da `Vacancy` em transação atômica.

---

### 🟣 Sprint / Marco 3 — Desenvolvimento II: Roteirização, Viagens & App do Monitor (Entrega: 17/02/2027)

#### US02 — Traçado Inteligente de Rota para Veículos Adaptados *(História Principal do PRD)*
- **História:** *Como* Gestor da Frota, *quero* que o sistema calcule e ordene a rota mais rápida considerando o endereço dos alunos cadeirantes e a escola de destino, *para* reduzir o tempo de permanência das crianças no veículo e economizar combustível.
- **Persona:** Eng. Fernando ("Nando")
- **Story Points:** `13` | **Prioridade:** `P0-critico` | **Responsáveis:** Rita, Erick (Back/OR-Tools) / Eduardo (Front/Leaflet)
- **Critérios de Aceite:**
  - [ ] Criação de `Route` e geração ordenada das paradas `RouteStop` (`sequence`) com estimativa de horário de chegada (ETA) em cada residência.
  - [ ] Validação transacional no backend impedindo alocar mais alunos cadeirantes do que o `wheelchair_capacity` do veículo ou alocar cadeirante em veículo sem elevador hidráulico.
  - [ ] Exibição visual do itinerário traçado no mapa (`react-leaflet`) no painel do gestor.

#### US08 — Escala e Execução de Viagens (`Trip`, `TripMonitor`, `TripStudent`)
- **História:** *Como* Gestor da Frota, *quero* programar viagens diárias associando rota, van adaptada, motorista, monitora(s) e lista de estudantes com vínculo `Enrollment` ativo, *para* disponibilizar a escala do dia para a equipe de campo.
- **Persona:** Eng. Fernando, Seu Valdir e Marisa ("Mari")
- **Story Points:** `8` | **Prioridade:** `P0-critico` | **Responsáveis:** Rita (Back) / Erick (Front)
- **Critérios de Aceite:**
  - [ ] Impedir sobreposição de horário para o mesmo veículo ou motorista.
  - [ ] Constraints de unicidade garantidas (`trip_id + student_id` e `trip_id + monitor_id`).
  - [ ] Fluxo de estado da viagem: `SCHEDULED` → `IN_PROGRESS` (`started_at`) → `COMPLETED` (`finished_at`).

#### US01 — Confirmação de Embarque e Desembarque em 1 Toque *(História Principal do PRD)*
- **História:** *Como* Monitora da Van Escolar, *quero* dar check-in no aluno ao embarcá-lo na rampa da van e check-out no desembarque com apenas 1 toque (ou QR Code), *para* registrar a presença com segurança e notificar a família e a escola instantaneamente.
- **Persona:** Marisa ("Mari")
- **Story Points:** `13` | **Prioridade:** `P0-critico` | **Responsáveis:** Eduardo, Erick (Front Mobile-First) / Rita (Back)
- **Critérios de Aceite:**
  - [ ] Interface mobile-first de alto contraste com botões grandes (`>= 56px` de altura) exibindo os alunos da viagem, foto/avatar, tipo de cadeira e parada.
  - [ ] Clique único registra `AttendanceEvent` (`BOARDING` ou `ALIGHTING`) com `occurred_at`, `monitor_id`, coordenadas GPS e `confirmation_method`.
  - [ ] Regra de integridade: não permitir `ALIGHTING` (check-out) de um aluno que não possua `BOARDING` (check-in) prévio na mesma viagem, nem permitir encerrar a viagem se ainda houver aluno embarcado sem check-out (prevenção de esquecimento no veículo).

#### US09 — Modo de Condução Simplificado para o Motorista
- **História:** *Como* Motorista de Van Adaptada, *quero* visualizar em tela limpa e com botões gigantes a próxima parada da rota, o nome do aluno e um atalho direto para navegação GPS por voz, *para* conduzir com segurança e sem distrações visuais.
- **Persona:** Seu Valdir ("Valdivino")
- **Story Points:** `5` | **Prioridade:** `P1-alta` | **Responsáveis:** Eduardo, Erick (Front)
- **Critérios de Aceite:**
  - [ ] Card em destaque da "Próxima Parada" (endereço legível em fonte grande, nome do aluno e badge se requer rampa elevatória).
  - [ ] Botão gigante para abrir coordenada da parada no navegador GPS com orientação por voz e botão de "Parada Concluída / Próxima".

---

### 🟠 Sprint / Marco 4 — Polimento, Tempo Real, Notificações & Nuvem (Entrega: 17/03/2027)

#### US10 — Rastreamento em Tempo Real e Notificações para a Família
- **História:** *Como* Mãe de aluno com deficiência, *quero* receber notificações imediatas quando meu filho embarcar na van e quando desembarcar na escola, além de acompanhar a posição da van no mapa em tempo real, *para* ter tranquilidade durante todo o trajeto.
- **Persona:** Solange ("Sol")
- **Story Points:** `13` | **Prioridade:** `P0-critico` | **Responsáveis:** Rita, Erick (Back/Realtime) / Eduardo (Front)
- **Critérios de Aceite:**
  - [ ] Disparo automático de registro em `Notification` (e push via FCM/Web Push) no instante do `AttendanceEvent` apenas para responsáveis com `can_receive_notifications = True`.
  - [ ] Tela da família com linha do tempo da viagem ("Van a caminho", "Embarcado às 07:12", "Chegou na escola às 07:35") e mapa atualizado com `VehicleLocation`.

#### US11 — Painel da Escola Receptora com Previsão de Chegada (ETA)
- **História:** *Como* Diretora de Escola Inclusiva, *quero* visualizar no portal web o horário estimado de chegada (ETA) de cada van escolar e quantos alunos cadeirantes estão a bordo, *para* posicionar os monitores internos no portão principal com antecedência.
- **Persona:** Profa. Tássia Camargo
- **Story Points:** `8` | **Prioridade:** `P1-alta` | **Responsáveis:** Eduardo, Erick (Front) / Rita (Back)
- **Critérios de Aceite:**
  - [ ] Painel de chegadas do turno exibindo código da van, status da viagem, ETA em minutos e contagem de alunos com cadeira de rodas / auxílio no desembarque.
  - [ ] Alerta visual quando a van estiver a menos de 5 minutos da escola.

#### US12 — Painel de Telemetria da Frota, Auditoria LGPD e Publicação em Nuvem
- **História:** *Como* Gestor da Frota Municipal, *quero* acompanhar todas as vans ativas no mapa com indicadores de quilometragem, tempo médio de viagem e trilha de auditoria (`AuditLog`), com o sistema publicado em nuvem, *para* comprovar a eficiência operacional da prefeitura.
- **Persona:** Eng. Fernando ("Nando")
- **Story Points:** `13` | **Prioridade:** `P0-critico` | **Responsáveis:** Todos os integrantes
- **Critérios de Aceite:**
  - [ ] Dashboard do Gestor com KPIs (total de alunos atendidos, vans em rota, redução estimada de km/tempo médio no veículo) e mapa geral da frota.
  - [ ] Registro de ações críticas em `AuditLog` sem vazamento de dados sensíveis.
  - [ ] Deploy funcional em nuvem (Frontend na Vercel, Backend Django no Render/Railway e PostgreSQL + PostGIS no Supabase).

---

## 5. Resumo de Pontuação por Marco (Velocity Planejada)

| Marco | Histórias | Total de Story Points |
| :--- | :--- | :---: |
| **Marco 1 (02/12/26)** | `US00.1`, `US00.2`, `US00.3`, `US00.4` | **21 pts** |
| **Marco 2 (13/01/27)** | `US03`, `US04`, `US05`, `US06`, `US07` | **34 pts** |
| **Marco 3 (17/02/27)** | `US01`, `US02`, `US08`, `US09` | **39 pts** |
| **Marco 4 (17/03/27)** | `US10`, `US11`, `US12` | **34 pts** |
| **Total do Projeto** | **16 Histórias / Entregáveis** | **128 pts** |
