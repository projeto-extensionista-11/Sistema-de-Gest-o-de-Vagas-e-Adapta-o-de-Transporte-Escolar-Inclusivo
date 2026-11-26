# 🚌 Projeto 11: Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo

> Módulo logístico integrado ao transporte público e escolar para gestão de vagas, otimização de rotas de vans adaptadas (com elevador hidráulico) e monitoramento em tempo real do embarque e desembarque de estudantes com deficiência (**LBI — Lei Brasileira de Inclusão, Art. 28 e 48**).

---

## 📌 1. Visão Geral e Negócio (BRD — O "Porquê")

### Problema Solucionado
A evasão escolar causada pela falta ou ineficiência do transporte adaptado, rotas mal planejadas que submetem estudantes a longas horas de deslocamento e a ausência de informações em tempo real para as famílias e escolas.

### Público-Alvo
- Gestores da frota do transporte escolar municipal/institucional
- Motoristas de vans adaptadas
- Monitores de transporte inclusivo
- Famílias e responsáveis de estudantes com deficiência
- Direções e equipes de apoio das escolas receptoras

### Metas de Negócio
- Garantir **100% de acesso** dos alunos com deficiência à escola.
- Otimizar custos de combustível e reduzir o tempo de permanência dos alunos nos veículos.
- Eliminar o risco de esquecimento de alunos no veículo por meio de check-in/check-out auditável.
- Dar transparência e tranquilidade às famílias com notificações e rastreamento em tempo real.

### Mapa de Processos (`As-Is` → `To-Be`)

| Cenário | Fluxo Operacional |
| :--- | :--- |
| **As-Is (Atual)** | Rotas de vans adaptadas organizadas em papel → Motorista faz trajeto por intuição → Atrasos de horas e alunos perdendo aulas → Pais sem notícias do embarque/desembarque. |
| **To-Be (Proposto)** | Solicitação de vaga no sistema → Otimizador logístico gera rota inteligente → Monitor marca check-in/check-out no app da van → Notificação em tempo real para pais, escola e gestão da frota. |

---

## 📋 2. Especificação do Sistema (PRD — O "O quê")

### Funcionalidades-Chave
- **Cadastro de Alunos e Necessidades Específicas:** Registro de tipo de cadeira de rodas, necessidade de acompanhante, auxílio no embarque e observações restritas de mobilidade.
- **Gestão de Vagas e Solicitações:** Controle de capacidade total e adaptada por escola/ano letivo, aprovação de solicitações e emissão de vínculo de transporte (`Enrollment`).
- **Otimizador de Rotas Logísticas:** Traçado inteligente considerando endereços, paradas e especificidades dos veículos adaptados.
- **App do Monitor (Check-in/Check-out):** Confirmação rápida de embarque e desembarque com 1 toque ou QR Code.
- **Painel de Frota e Telemetria:** Monitoramento da frota em mapa em tempo real, indicadores operacionais e previsão de chegada na escola.

### Histórias de Usuário e Critérios de Aceite

- **US01 — Confirmação de Embarque/Desembarque**
  - *Como* Monitor da Van Escolar, *quero* dar check-in no aluno ao embarcá-lo na rampa da van, *para* notificar a família e a escola instantaneamente.
  - *Critérios de Aceite:* Clique único no nome do aluno no app do monitor; envio imediato de notificação push para os responsáveis autorizados.
- **US02 — Traçado Inteligente de Rota**
  - *Como* Gestor da Frota, *quero* que o sistema calcule a rota mais rápida considerando o endereço de 8 alunos cadeirantes, *para* reduzir o tempo de permanência no veículo.
  - *Critérios de Aceite:* Geração automática de itinerário no mapa com estimativa de horário de chegada em cada residência e na escola.

### Personas Atendidas

| Persona | Perfil | Necessidade Principal no Sistema |
| :--- | :--- | :--- |
| **Seu Valdir ("Valdivino")** | Motorista (56 anos, 18 anos de experiência) | Interface limpa, botões grandes e rota integrada com orientação clara sem distrações ao dirigir. |
| **Marisa ("Mari")** | Monitora da Van (38 anos) | App móvel ágil para check-in/check-out com **1 toque** após operar a rampa elevatória e o cinto. |
| **Solange ("Sol")** | Mãe de aluno com tetraplegia (32 anos) | Rastreamento da van no mapa em tempo real e notificações push imediatas de embarque e chegada. |
| **Eng. Fernando ("Nando")** | Gestor da Frota Municipal (45 anos) | Painel com telemetria em mapa, otimização de rotas, redução de quilometragem e tempo médio de viagem. |
| **Profa. Tássia Camargo** | Diretora de Escola Inclusiva (48 anos) | Portal web com horário estimado de chegada das vans para organizar os monitores de recepção no portão. |

---

## 👥 3. Equipe e Divisão de Papéis

| Área | Integrantes | Responsabilidades |
| :--- | :--- | :--- |
| **Frontend** | Eduardo, Erick | Desenvolvimento da interface web e mobile-first (painéis, formulários, acessibilidade, mapas e consumo da API) |
| **Backend** | Rita, Erick | API REST, regras de negócio, autenticação/permissões, otimização de rotas, tempo real e notificações |
| **Banco de Dados e Modelagem** | Edmilson, Karina, Eduardo | Modelagem conceitual/lógica (DER), dicionário de dados, integridade relacional, PostGIS e políticas de proteção de dados |

---

## 🛠️ 4. Stack Tecnológica e Justificativas (Por que cada tecnologia?)

A arquitetura segue o modelo de **monólito modular no backend** (centralizando regras de negócio, validações e LGPD na API) integrado a um **frontend tipado e responsivo** e a um **banco relacional com extensão geoespacial**.

### 🎨 Frontend

| Tecnologia | Por que usar neste projeto? |
| :--- | :--- |
| **Next.js (App Router) + React** | Permite construir em uma única base tanto os painéis administrativos (Gestor e Diretora) quanto as visões leves para celular (Monitor e Famílias), com roteamento organizado, boa performance e suporte nativo a SSR/CSR. |
| **TypeScript** | Garante segurança de tipos nas estruturas de dados complexas do domínio (alunos, necessidades de mobilidade, rotas, viagens e eventos), reduzindo bugs de integração entre Frontend e Backend. |
| **Tailwind CSS** | Acelera a construção de interfaces consistentes e responsivas (desktop para gestão e mobile-first com botões grandes para o motorista/monitor em campo). |
| **shadcn/ui (Radix UI)** | Como o projeto trata de **transporte inclusivo**, a acessibilidade da interface é indispensável. O `shadcn/ui` oferece componentes acessíveis por padrão (navegação por teclado, foco e atributos ARIA) e totalmente customizáveis com Tailwind. |
| **TanStack Query (React Query)** | Gerencia cache, estados de carregamento/erro e atualização automática de dados dinâmicos (status de solicitações, lista de embarque e telemetria) sem código repetitivo. |
| **React Hook Form + Zod** | O sistema possui cadastros sensíveis e extensos (`Student`, `Guardian`, `StudentNeed`, `Vehicle`). Essa dupla garante formulários performáticos e validação rigorosa no cliente antes de enviar à API. |
| **Leaflet (`react-leaflet`) / Mapbox** | Essencial para renderizar escolas, paradas (`RouteStop`), itinerários e a posição em tempo real das vans (`VehicleLocation`) no painel do gestor e no acompanhamento das famílias. |
| **PWA / Mobile-first + QR Code** | Permite que a monitora utilize o fluxo de check-in/check-out diretamente pelo navegador do celular com experiência semelhante a aplicativo nativo, botões de 1 toque e suporte opcional a leitura de QR Code. |
| **Lucide Icons + ESLint + Prettier** | Padronização visual (ícones claros para usuários com letramento digital básico) e consistência de código entre toda a equipe de frontend. |

### ⚙️ Backend

| Tecnologia | Por que usar neste projeto? |
| :--- | :--- |
| **Python + Django + Django REST Framework (DRF)** | Produtividade alta para construir um monólito modular bem estruturado, com ORM maduro, sistema de migrations robusto, autenticação customizada e controle fino de permissões por perfil e organização. |
| **Django Channels ou Supabase Realtime** *(em avaliação)* | Necessário para transmitir atualizações de localização GPS e eventos de check-in/check-out em tempo real para o painel do gestor, escola e responsáveis sem polling excessivo. |
| **Google OR-Tools** *(em avaliação)* | Biblioteca referência para problemas de roteamento de veículos (VRP), ideal para calcular rotas otimizadas considerando múltiplas paradas, capacidade de passageiros e vagas para cadeiras de rodas. |
| **Firebase Cloud Messaging (FCM)** *(proposta)* | Entrega confiável de notificações push em dispositivos móveis para alertar os responsáveis no instante do embarque e desembarque. |

### 🗄️ Banco de Dados e Infraestrutura

| Tecnologia | Por que usar neste projeto? |
| :--- | :--- |
| **PostgreSQL (via Supabase)** | Banco relacional sólido, com suporte a transações ACID, constraints de unicidade/integridade e chaves estrangeiras indispensáveis para evitar duplicidade de alunos, conflitos de viagem ou falhas de alocação de vagas. |
| **PostGIS** | Extensão geoespacial padrão de mercado para armazenar coordenadas (`SRID 4326`), calcular distâncias, indexar pontos de parada e consultar posições dos veículos com eficiência. |
| **Supabase Storage** *(opcional)* | Armazenamento privado de documentos e arquivos com controle de acesso por URLs assinadas, mantendo dados sensíveis protegidos. |

---

## 🧩 5. Domínios e Modelagem Inicial (v0.1)

O banco de dados e a arquitetura modular do Django são organizados em 6 domínios principais (detalhes completos, dicionário de dados, cardinalidades e critérios de aceite disponíveis em [`docs/modelagem-arquitetura-v0.1.md`](docs/modelagem-arquitetura-v0.1.md)):

| Domínio | Entidades Principais | Responsabilidade |
| :--- | :--- | :--- |
| **Administração e Acesso** | `Organization`, `User`, `OrganizationMembership` | Multi-organização (prefeituras/instituições), usuário customizado e papéis de acesso. |
| **Alunos e Responsáveis** | `Student`, `Guardian`, `StudentGuardian`, `StudentNeed` | Cadastro de alunos, relação N:N com responsáveis autorizados e necessidades específicas de deslocamento. |
| **Escolas e Vagas** | `School`, `Vacancy`, `TransportRequest`, `Enrollment` | Capacidade total e adaptada por escola, fluxo de aprovação de pedidos e vínculo ativo de transporte. |
| **Frota** | `Vehicle`, `Driver`, `Monitor` | Veículos (capacidade comum, vagas para cadeira de rodas, elevador hidráulico), CNH de motoristas e monitores. |
| **Logística** | `Route`, `RouteStop`, `Trip`, `TripMonitor`, `TripStudent` | Rotas reutilizáveis, paradas ordenadas, execução de viagens, alocação de monitores e alunos. |
| **Operação e Monitoramento** | `AttendanceEvent`, `VehicleLocation`, `Notification`, `AuditLog` | Eventos auditáveis de embarque/desembarque (`BOARDING`/`ALIGHTING`), histórico GPS, avisos e auditoria. |

---

## 🔒 6. Segurança, Privacidade e LGPD

Por tratar **dados pessoais de crianças** e **informações sensíveis de saúde/deficiência e geolocalização**, a arquitetura adota:

- **Menor privilégio e isolamento multi-organização:** Toda requisição na API valida o vínculo organizacional e o papel do usuário.
- **Restrição de acesso a `StudentNeed`:** Informações de mobilidade e observações médicas ficam acessíveis apenas a quem tem necessidade operacional.
- **Nenhuma exposição direta do banco ao frontend:** Chaves privilegiadas do Supabase nunca vão para o cliente; toda regra crítica passa pelo backend Django.
- **Auditoria e retenção:** Registro de ações relevantes em `AuditLog` (sem expor dados sensíveis em logs ou notificações de tela bloqueada) e política de retenção para histórico de GPS.

---

## 📑 7. Índice de Documentação e Entregáveis (Marco 1 — M1)

Todos os artefatos exigidos no **Marco 1 (Imersão no Problema, Planejamento da Arquitetura e Setup da Engenharia)** estão versionados e organizados neste repositório:

| Entregável do Marco 1 | Arquivo / Localização | Conteúdo Principal |
| :--- | :--- | :--- |
| **1. BRD, PRD e Personas** | [`docs/premissa_projeto.md`](docs/premissa_projeto.md) | Problema, metas (LBI Art. 28 e 48), mapa `As-Is / To-Be`, `US01`, `US02` e as 5 Personas. |
| **2. Arquitetura, ADRs e C4** | [`docs/ADR-arquitetura.md`](docs/ADR-arquitetura.md) | `ADR-001` a `ADR-005` + Diagramas C4 (Contexto, Contêineres, Módulos Django e Infraestrutura Cloud). |
| **3. Modelagem de Dados (v0.1)** | [`docs/modelagem-arquitetura-v0.1.md`](docs/modelagem-arquitetura-v0.1.md) | Dicionário completo das 23 entidades em 6 domínios, cardinalidades, regras de integridade e LGPD. |
| **4. Backlog, Sprints e Kanban** | [`docs/backlog-sprint-planning.md`](docs/backlog-sprint-planning.md) | Épicos, 16 Histórias de Usuário/Entregáveis pontuadas em *Story Points* (Fibonacci) e distribuídas nos Marcos M1 a M4. |
| **5. Wireframes e Fluxo de Telas** | [`docs/wireframes-fluxo-telas.md`](docs/wireframes-fluxo-telas.md) & [🎨 Protótipo no Figma](https://www.figma.com/design/caIccM5BeNfomthMS7QNVl) | Fluxo de navegação e protótipos de baixa/média fidelidade para as 5 Personas (Monitora, Motorista, Mãe, Gestor e Diretora). |
| **6. Design System & Figma MCP** | [`docs/design-system-rules.md`](docs/design-system-rules.md) & [`CLAUDE.md`](CLAUDE.md) | Tokens de alto contraste (WCAG AA/AAA), componentes `shadcn/ui`, alvos de toque `>= 56px` e regras Figma-to-Code. |
| **7. Governança e GitFlow** | [`CONTRIBUTING.md`](CONTRIBUTING.md) & [`LICENSE`](LICENSE) | Estratégia de branches (`main`, `develop`, `feature/*`), Conventional Commits, Code Review e Licença MIT. |
| **8. Templates de Colaboração** | [`.github/PULL_REQUEST_TEMPLATE.md`](.github/PULL_REQUEST_TEMPLATE.md) & [`.github/ISSUE_TEMPLATE/user_story.md`](.github/ISSUE_TEMPLATE/user_story.md) | Padronização automática de Pull Requests e cadastro de Histórias de Usuário no GitHub Issues/Projects. |

---

## 📁 8. Estrutura do Repositório

```text
.
├── .github/                  # Governança de Engenharia Colaborativa
│   ├── ISSUE_TEMPLATE/
│   │   └── user_story.md     # Template padrão de História de Usuário para o Kanban
│   └── PULL_REQUEST_TEMPLATE.md
├── backend/                  # API modular em Django + DRF (planejada para M2..M4)
│   ├── config/
│   ├── apps/
│   │   ├── accounts/
│   │   ├── organizations/
│   │   ├── students/
│   │   ├── guardians/
│   │   ├── schools/
│   │   ├── vacancies/
│   │   ├── fleet/
│   │   ├── routes/
│   │   ├── trips/
│   │   ├── tracking/
│   │   ├── notifications/
│   │   └── audit/
│   └── manage.py
├── docs/                     # Documentação Técnica, Arquitetura, Backlog e UX
│   ├── ADR-arquitetura.md
│   ├── backlog-sprint-planning.md
│   ├── design-system-rules.md
│   ├── modelagem-arquitetura-v0.1.md
│   ├── premissa_projeto.md
│   └── wireframes-fluxo-telas.md
├── frontend/                 # Aplicação Next.js + TypeScript + Tailwind + shadcn/ui
├── .gitignore                # Exclusão de artefatos Python, Node, .env e IDEs
├── CLAUDE.md                 # Regras de contexto e integração Figma MCP
├── CONTRIBUTING.md           # Guia de GitFlow, Conventional Commits e Code Review
├── LICENSE                   # Licença de Software Livre (MIT)
└── README.md                 # Visão executiva do projeto
```

---

## 🗺️ 9. Cronograma de Marcos e Entregas

- [x] **MÊS 01 — Iniciação & Setup (Entrega: 02/12/2026 | Peso: 10%)**
  - [x] Definição de equipe, BRD/PRD, personas e histórias de usuário
  - [x] Modelagem de dados v0.1 (23 entidades) e documento de ADRs + Diagramas C4
  - [x] Estruturação do repositório GitHub (`README`, `CONTRIBUTING`, `LICENSE`, GitFlow `main`/`develop` e templates)
  - [x] Backlog do produto mapeado em Sprints com *Story Points*
  - [x] Regras do Design System inclusivo e especificação de Wireframes das 5 personas
- [ ] **MÊS 02 — Desenvolvimento I (Entrega: 13/01/2027 | Peso: 15%)**
  - [ ] Setup PostgreSQL + PostGIS, autenticação multi-perfil (`US03`) e cadastros base (`US04` a `US07`)
- [ ] **MÊS 03 — Desenvolvimento II (Entrega: 17/02/2027 | Peso: 15%)**
  - [ ] Otimizador de rotas (`US02`), gestão de viagens (`US08`), App do Monitor com Check-in/Out em 1 toque (`US01`) e painel do motorista (`US09`)
- [ ] **MÊS 04 — Polimento & Publicação em Nuvem (Entrega: 17/03/2027 | Peso: 60%)**
  - [ ] Rastreamento em tempo real e notificações push para famílias (`US10`), painel da escola (`US11`), telemetria/auditoria LGPD e deploy Cloud (`US12`)

---

## 📚 Referências Técnicas

- [Next.js Documentation](https://nextjs.org/docs)
- [shadcn/ui](https://ui.shadcn.com/)
- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Supabase & PostGIS](https://supabase.com/docs/guides/database/extensions/postgis)
- [Google OR-Tools](https://developers.google.com/optimization)
