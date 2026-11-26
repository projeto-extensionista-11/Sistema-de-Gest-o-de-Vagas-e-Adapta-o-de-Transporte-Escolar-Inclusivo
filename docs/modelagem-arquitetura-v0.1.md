# Modelagem de Dados e Arquitetura Inicial

**Projeto:** Sistema de Gestão de Vagas e Transporte Escolar Inclusivo  
**Status:** Documento para discussão e validação da equipe  
**Versão:** 0.1 | **Data:** 26 de novembro de 2026

---

## 1. Objetivo e Contexto

Este documento consolida uma proposta inicial de modelagem de dados e arquitetura para o Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo. O objetivo é organizar as informações necessárias para cadastro de estudantes e responsáveis, gestão escolar, frota, solicitações de vagas, planejamento de rotas, execução de viagens, embarque/desembarque e acompanhamento da operação.

A proposta parte dos requisitos descritos no documento do projeto: transporte escolar adaptado, veículos com elevador hidráulico, necessidades específicas de deslocamento, aplicativo do monitor para check-in/check-out, painel de gestão de frota, localização em mapa e notificações às famílias.

### 1.1 Objetivos de negócio identificados
- Organizar solicitações e alocação de vagas no transporte escolar inclusivo.
- Planejar rotas considerando endereços, horários e características dos veículos.
- Registrar embarque e desembarque dos estudantes com confirmação operacional.
- Informar responsáveis e gestão sobre eventos relevantes da viagem.
- Acompanhar a localização da frota e apoiar a gestão operacional.

### 1.2 Escopo funcional considerado

| Domínio | Responsabilidade principal |
| :--- | :--- |
| **Administração e acesso** | Organizações, usuários, vínculos e perfis de acesso. |
| **Alunos e responsáveis** | Cadastro dos estudantes, contatos e necessidades de deslocamento. |
| **Escolas e vagas** | Instituições, capacidade disponível, solicitações e vínculos aprovados. |
| **Frota** | Veículos, motoristas e monitores. |
| **Logística** | Rotas, paradas, viagens e estudantes alocados. |
| **Operação e monitoramento** | Eventos de embarque/desembarque, GPS, notificações e auditoria. |

---

## 2. Decisões Arquiteturais Iniciais

| Camada | Tecnologia proposta | Papel |
| :--- | :--- | :--- |
| **Backend** | Python + Django + Django REST Framework | API, regras de negócio, validações, permissões e integrações. |
| **Banco de dados** | PostgreSQL gerenciado pelo Supabase | Persistência relacional principal. |
| **Geodados** | PostGIS | Consultas espaciais, pontos, distâncias e dados geográficos. |
| **Arquivos** | Supabase Storage (opcional) | Documentos e imagens, com políticas privadas de acesso. |
| **Tempo real** | Supabase Realtime ou Django Channels (a decidir) | Atualizações de localização e eventos em tempo real. |
| **Notificações push** | Firebase Cloud Messaging (proposta) | Entrega de notificações aos aplicativos. |
| **Otimização de rotas** | OR-Tools + serviço de roteamento (a avaliar) | Otimização e cálculo de itinerários. |

> **Diretriz:** começar com um monólito modular Django. O Django será a fonte principal das regras de negócio e das operações sensíveis. O Supabase será utilizado prioritariamente como PostgreSQL gerenciado e, se aprovado, para Storage e recursos complementares. Evitar duplicar regras de negócio entre Django e acesso direto do frontend ao banco.

A escolha final entre Supabase, Neon, Render ou outro provedor deve considerar custo, região de hospedagem, backups, recuperação, suporte a PostGIS, limites de conexão, disponibilidade e requisitos de proteção de dados.

---

## 3. Premissas e Decisões Ainda Sujeitas à Validação

- A proposta considera suporte futuro a múltiplas prefeituras/organizações. A abrangência real do primeiro lançamento precisa ser confirmada.
- Cada viagem terá um motorista responsável e poderá ter um ou mais monitores. Confirmar se esse cenário corresponde à operação real.
- Motorista e monitor são perfis funcionais distintos, vinculados a uma conta `User` para autenticação.
- O vínculo de transporte aprovado (`Enrollment`) autoriza a participação do estudante em viagens.
- O estudante pode ter mais de um responsável; um responsável pode estar vinculado a mais de um estudante.
- As necessidades específicas do estudante devem ter acesso restrito e não devem ser expostas a usuários sem necessidade operacional.
- A modelagem apresentada é uma base de discussão, não um esquema pronto para produção. Tipos, nulabilidade, restrições, índices e políticas precisam ser revisados.

---

## 4. Modelo Conceitual: Domínios e Entidades

| Domínio | Entidades propostas |
| :--- | :--- |
| **Administração** | `Organization`, `User`, `OrganizationMembership` |
| **Alunos e responsáveis** | `Student`, `Guardian`, `StudentGuardian`, `StudentNeed` |
| **Escolas e vagas** | `School`, `Vacancy`, `TransportRequest`, `Enrollment` |
| **Frota** | `Vehicle`, `Driver`, `Monitor` |
| **Logística** | `Route`, `RouteStop`, `Trip`, `TripMonitor`, `TripStudent` |
| **Monitoramento** | `AttendanceEvent`, `VehicleLocation`, `Notification`, `AuditLog` |

---

## 5. Dicionário Inicial de Dados

Os tipos são sugestões compatíveis com PostgreSQL. `UUID` é recomendado como identificador primário. Campos comuns como `created_at` e `updated_at` podem ser padronizados em uma classe base no Django.

### 5.1 `Organization` — Organização
Representa a prefeitura ou instituição responsável pela operação.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `name` | `VARCHAR(255)` | Nome |
| `cnpj` | `VARCHAR(18)` | Identificação fiscal; validar obrigatoriedade |
| `organization_type` | `VARCHAR(30)` | Tipo de organização |
| `is_active` | `BOOLEAN` | Situação |
| `created_at` | `TIMESTAMP` | Criação |

### 5.2 `User` — Usuário
Conta de autenticação central. No Django, implementar como usuário customizado desde o início.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `full_name` | `VARCHAR(255)` | Nome |
| `email` | `VARCHAR(255)` | Login/e-mail; avaliar unicidade global |
| `password` | `HASH` | Nunca armazenar senha em texto puro; usar mecanismo do Django |
| `phone` | `VARCHAR(20)` | Telefone |
| `is_active` | `BOOLEAN` | Situação |
| `created_at` | `TIMESTAMP` | Criação |

### 5.3 `OrganizationMembership` — Vínculo de usuário
Relaciona usuários às organizações e define o papel de acesso em cada organização.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `organization_id` | `UUID (FK)` | Organização |
| `user_id` | `UUID (FK)` | Usuário |
| `role` | `VARCHAR(30)` | Papel/perfil |
| `is_active` | `BOOLEAN` | Situação |
| `UNIQUE` | `organization_id + user_id` | Evita vínculo duplicado |

### 5.4 `Student` — Aluno
Cadastro do estudante atendido pelo serviço.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `organization_id` | `UUID (FK)` | Organização gestora |
| `full_name` | `VARCHAR(255)` | Nome completo |
| `birth_date` | `DATE` | Nascimento |
| `registration_number` | `VARCHAR(50)` | Matrícula/código local |
| `is_active` | `BOOLEAN` | Situação |
| `created_at` | `TIMESTAMP` | Criação |

### 5.5 `Guardian` — Responsável
Pessoa responsável pelo estudante; pode possuir conta de acesso opcional.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `user_id` | `UUID (FK, opcional, UNIQUE)` | Conta de acesso, se houver |
| `full_name` | `VARCHAR(255)` | Nome |
| `cpf` | `VARCHAR(14)` | Dado pessoal; proteger acesso |
| `phone` | `VARCHAR(20)` | Contato |
| `email` | `VARCHAR(255)` | Contato |
| `is_active` | `BOOLEAN` | Situação |

### 5.6 `StudentGuardian` — Relação aluno/responsável
Tabela associativa N:N, com atributos próprios do vínculo.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `student_id` | `UUID (FK)` | Aluno |
| `guardian_id` | `UUID (FK)` | Responsável |
| `relationship` | `VARCHAR(50)` | Parentesco/relação |
| `is_primary` | `BOOLEAN` | Responsável principal |
| `can_receive_notifications` | `BOOLEAN` | Autorização para notificações |
| `UNIQUE` | `student_id + guardian_id` | Evita duplicidade |

### 5.7 `StudentNeed` — Necessidades de deslocamento
Registra informações necessárias à adaptação do transporte. A proposta inicial prevê um registro por aluno; avaliar histórico e múltiplas necessidades.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `student_id` | `UUID (FK, UNIQUE)` | Aluno |
| `wheelchair_type` | `VARCHAR(50)` | Tipo de cadeira, se aplicável |
| `requires_companion` | `BOOLEAN` | Necessita acompanhante |
| `boarding_assistance` | `BOOLEAN` | Necessita auxílio no embarque |
| `mobility_notes` | `TEXT` | Observações restritas |

### 5.8 `School` — Escola
Instituição de ensino atendida pela organização.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `organization_id` | `UUID (FK)` | Organização |
| `name` | `VARCHAR(255)` | Nome |
| `address` | `TEXT` | Endereço |
| `latitude/longitude` | `DECIMAL(10,7)` | Coordenadas iniciais; considerar `PointField`/PostGIS |
| `phone` | `VARCHAR(20)` | Contato |
| `is_active` | `BOOLEAN` | Situação |

### 5.9 `Vacancy` — Vaga
Capacidade de atendimento disponibilizada por escola e período letivo.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `school_id` | `UUID (FK)` | Escola |
| `school_year` | `INTEGER` | Ano letivo |
| `total_capacity` | `INTEGER` | Capacidade total |
| `accessible_capacity` | `INTEGER` | Capacidade adaptada |
| `status` | `VARCHAR(30)` | Situação |

### 5.10 `TransportRequest` — Solicitação de transporte
Pedido de atendimento para um estudante.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `student_id` | `UUID (FK)` | Aluno |
| `school_id` | `UUID (FK)` | Escola |
| `vacancy_id` | `UUID (FK, opcional)` | Vaga pretendida, se aplicável |
| `pickup_address` | `TEXT` | Endereço de embarque |
| `pickup_latitude/longitude` | `DECIMAL(10,7)` | Coordenadas; considerar PostGIS |
| `status` | `VARCHAR(30)` | `PENDING`, `UNDER_REVIEW`, `APPROVED`, `REJECTED`, `CANCELLED` |
| `requested_at` | `TIMESTAMP` | Data do pedido |

### 5.11 `Enrollment` — Vínculo de transporte
Representa a autorização ativa do estudante para utilizar o serviço.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `student_id` | `UUID (FK)` | Aluno |
| `school_id` | `UUID (FK)` | Escola |
| `request_id` | `UUID (FK, UNIQUE)` | Solicitação que originou o vínculo |
| `start_date` | `DATE` | Início |
| `end_date` | `DATE` | Fim, se houver |
| `status` | `VARCHAR(30)` | Situação |

### 5.12 `Vehicle` — Veículo
Cadastro da frota disponível.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `organization_id` | `UUID (FK)` | Organização |
| `plate` | `VARCHAR(10)` | Placa; unicidade por escopo a validar |
| `model` | `VARCHAR(100)` | Modelo |
| `brand` | `VARCHAR(100)` | Marca |
| `manufacturing_year` | `INTEGER` | Ano |
| `passenger_capacity` | `INTEGER` | Capacidade de passageiros |
| `wheelchair_capacity` | `INTEGER` | Capacidade para cadeiras |
| `has_hydraulic_lift` | `BOOLEAN` | Possui elevador |
| `is_active` | `BOOLEAN` | Situação |

### 5.13 `Driver` — Motorista
Perfil funcional de motorista, vinculado a uma conta `User`.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `user_id` | `UUID (FK, UNIQUE)` | Conta de acesso |
| `organization_id` | `UUID (FK)` | Organização |
| `cpf` | `VARCHAR(14)` | Dado pessoal |
| `cnh_number` | `VARCHAR(30)` | CNH |
| `cnh_category` | `VARCHAR(5)` | Categoria |
| `cnh_expiration` | `DATE` | Validade |
| `is_active` | `BOOLEAN` | Situação |

### 5.14 `Monitor` — Monitor
Perfil funcional de monitor, vinculado a uma conta `User`.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `user_id` | `UUID (FK, UNIQUE)` | Conta de acesso |
| `organization_id` | `UUID (FK)` | Organização |
| `cpf` | `VARCHAR(14)` | Dado pessoal |
| `phone` | `VARCHAR(20)` | Contato |
| `is_active` | `BOOLEAN` | Situação |

### 5.15 `Route` — Rota
Definição lógica de um itinerário, reutilizável em diferentes viagens.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `organization_id` | `UUID (FK)` | Organização |
| `name` | `VARCHAR(255)` | Nome |
| `description` | `TEXT` | Descrição |
| `is_active` | `BOOLEAN` | Situação |

### 5.16 `RouteStop` — Parada da rota
Ponto ordenado de embarque, desembarque ou referência dentro de uma rota.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `route_id` | `UUID (FK)` | Rota |
| `name` | `VARCHAR(255)` | Nome |
| `address` | `TEXT` | Endereço |
| `latitude/longitude` | `DECIMAL(10,7)` | Coordenadas; considerar `PointField` |
| `stop_type` | `VARCHAR(30)` | Tipo de parada |
| `sequence` | `INTEGER` | Ordem na rota |

### 5.17 `Trip` — Viagem
Execução de uma rota em determinado dia/horário, com veículo e motorista definidos.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `route_id` | `UUID (FK)` | Rota |
| `vehicle_id` | `UUID (FK)` | Veículo |
| `driver_id` | `UUID (FK)` | Motorista |
| `departure_time` | `TIMESTAMP` | Partida planejada |
| `started_at` | `TIMESTAMP` | Início real |
| `finished_at` | `TIMESTAMP` | Fim real |
| `status` | `VARCHAR(30)` | Situação |

### 5.18 `TripMonitor` — Monitores da viagem
Tabela associativa que permite um ou mais monitores por viagem.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `trip_id` | `UUID (FK)` | Viagem |
| `monitor_id` | `UUID (FK)` | Monitor |
| `UNIQUE` | `trip_id + monitor_id` | Evita duplicidade |

### 5.19 `TripStudent` — Alunos da viagem
Associa os estudantes autorizados a uma viagem e define pontos de embarque e desembarque.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `trip_id` | `UUID (FK)` | Viagem |
| `student_id` | `UUID (FK)` | Aluno |
| `enrollment_id` | `UUID (FK)` | Vínculo de transporte |
| `pickup_stop_id` | `UUID (FK)` | Parada de embarque |
| `dropoff_stop_id` | `UUID (FK)` | Parada de desembarque |
| `UNIQUE` | `trip_id + student_id` | Evita duplicidade na viagem |

### 5.20 `AttendanceEvent` — Evento de embarque/desembarque
Registro imutável ou auditável de cada evento operacional.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `trip_student_id` | `UUID (FK)` | Aluno na viagem |
| `monitor_id` | `UUID (FK)` | Monitor que registrou |
| `event_type` | `VARCHAR(20)` | `BOARDING` ou `ALIGHTING` |
| `occurred_at` | `TIMESTAMP` | Horário do evento |
| `latitude/longitude` | `DECIMAL(10,7)` | Coordenadas do evento, se disponíveis |
| `confirmation_method` | `VARCHAR(20)` | Toque, QR Code ou outro método |

### 5.21 `VehicleLocation` — Localização do veículo
Pontos de GPS associados à viagem. Espera-se volume elevado de registros.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `trip_id` | `UUID (FK)` | Viagem |
| `latitude/longitude` | `DECIMAL(10,7)` | Coordenadas; preferir tipo geográfico PostGIS quando adequado |
| `speed` | `DECIMAL(8,2)` | Velocidade, com unidade definida |
| `recorded_at` | `TIMESTAMP` | Horário de captura |

### 5.22 `Notification` — Notificação
Mensagem destinada a um usuário, por exemplo um responsável autorizado.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `recipient_user_id` | `UUID (FK)` | Destinatário |
| `student_id` | `UUID (FK, opcional)` | Aluno relacionado |
| `title` | `VARCHAR(255)` | Título |
| `message` | `TEXT` | Conteúdo |
| `notification_type` | `VARCHAR(50)` | Tipo |
| `read_at` | `TIMESTAMP, opcional` | Leitura |
| `created_at` | `TIMESTAMP` | Criação |

### 5.23 `AuditLog` — Auditoria
Registra ações relevantes de usuários para rastreabilidade.

| Campo | Tipo sugerido | Observação |
| :--- | :--- | :--- |
| `id` | `UUID (PK)` | Identificador |
| `organization_id` | `UUID (FK)` | Organização |
| `actor_user_id` | `UUID (FK, opcional)` | Autor da ação |
| `action` | `VARCHAR(50)` | Ação |
| `entity_type` | `VARCHAR(100)` | Tipo de entidade |
| `entity_id` | `UUID` | Registro afetado |
| `created_at` | `TIMESTAMP` | Data/hora |

---

## 6. Relacionamentos e Cardinalidades

| Origem | Relacionamento | Destino | Interpretação |
| :--- | :--- | :--- | :--- |
| `Organization` | `1:N` | `Student` / `School` / `Vehicle` / `Driver` / `Monitor` / `Route` | Uma organização gerencia vários registros desses tipos. |
| `User` | `1:N` | `OrganizationMembership` | Um usuário pode ter vínculo com várias organizações. |
| `Student` | `N:N` | `Guardian` | Por meio de `StudentGuardian`; ambos podem ter múltiplos vínculos. |
| `Student` | `1:0..1` | `StudentNeed` | Proposta inicial de um registro de necessidades por aluno. |
| `School` | `1:N` | `Vacancy` | Uma escola disponibiliza várias vagas/períodos. |
| `Student` | `1:N` | `TransportRequest` | Um aluno pode solicitar transporte em momentos distintos. |
| `TransportRequest` | `1:0..1` | `Enrollment` | Uma solicitação aprovada pode originar um vínculo. |
| `Route` | `1:N` | `RouteStop` | Uma rota contém paradas ordenadas. |
| `Route` | `1:N` | `Trip` | Uma rota pode ser executada em várias viagens. |
| `Vehicle` / `Driver` | `1:N` | `Trip` | Veículo e motorista podem participar de várias viagens ao longo do tempo. |
| `Trip` | `N:N` | `Monitor` | Por meio de `TripMonitor`. |
| `Trip` | `N:N` | `Student` | Por meio de `TripStudent`, com vínculo de transporte e paradas. |
| `TripStudent` | `1:N` | `AttendanceEvent` | Cada aluno na viagem pode ter eventos de embarque/desembarque. |
| `Trip` | `1:N` | `VehicleLocation` | Uma viagem registra várias posições GPS. |

---

## 7. Regras de Negócio e Integridade

| Regra proposta | Como garantir |
| :--- | :--- |
| Não duplicar vínculo de usuário na organização. | Unique constraint em `organization_id + user_id`. |
| Não duplicar aluno na mesma viagem. | Unique constraint em `trip_id + student_id`. |
| Não duplicar monitor na mesma viagem. | Unique constraint em `trip_id + monitor_id`. |
| Viagem deve ter veículo e motorista. | FKs obrigatórias e validação de estado. |
| Respeitar capacidade do veículo e capacidade adaptada. | Validação transacional no serviço de alocação; não apenas no frontend. |
| Aluno deve possuir vínculo de transporte válido para a viagem. | Validação de `Enrollment` e período de validade. |
| Check-out deve corresponder a um embarque anterior. | Regra de domínio/serviço e consistência dos eventos. |
| Somente responsáveis autorizados recebem avisos do estudante. | Autorização baseada em `StudentGuardian` e permissões. |
| Evitar sobreposição de viagens para veículo e motorista. | Validação de conflito de horários; considerar constraint/exclusion no PostgreSQL quando aplicável. |
| Paradas de embarque/desembarque devem pertencer à rota da viagem. | Validação de integridade no serviço e, se possível, constraints. |
| Registros sensíveis devem ter acesso mínimo e rastreabilidade. | Permissões, auditoria, logs sem dados excessivos e políticas de acesso. |

---

## 8. Segurança, Privacidade e LGPD

O sistema tratará dados pessoais de crianças e dados referentes à saúde/deficiência ou necessidades específicas de deslocamento. Esses dados exigem tratamento especialmente cuidadoso. A equipe deve definir finalidade, base legal adequada, transparência aos responsáveis, retenção e descarte, além de medidas técnicas e administrativas de segurança. A base legal e os avisos de privacidade devem ser validados com orientação jurídica ou responsável institucional.

- Aplicar menor privilégio: cada perfil acessa somente o necessário para sua função.
- Separar os dados por organização e verificar a organização em toda operação da API.
- Não expor chaves privilegiadas do Supabase no frontend.
- Manter tabelas e buckets privados; usar URLs assinadas quando necessário.
- Proteger dados de localização, especialmente quando associados a crianças e rotinas escolares.
- Evitar incluir dados sensíveis em logs, mensagens de erro ou notificações de tela bloqueada.
- Registrar acessos e alterações relevantes sem armazenar conteúdo sensível desnecessário.
- Definir política de retenção para histórico de GPS e eventos operacionais.
- Criptografar tráfego com TLS e proteger backups e credenciais.

---

## 9. Geolocalização e Rastreamento

Para endereços, escolas, paradas e posições de veículos, recomenda-se avaliar PostGIS e o tipo geográfico `Point`. O modelo inicial usa latitude/longitude separados apenas para facilitar a leitura; a implementação deve padronizar o sistema de referência (por exemplo, `SRID 4326`), a ordem longitude/latitude e a validação de coordenadas.

- Definir frequência de envio do GPS e comportamento quando não houver conexão.
- Distinguir localização atual da última localização conhecida.
- Definir retenção e eventual agregação/arquivamento do histórico.
- Criar índices espaciais para consultas geográficas.
- Não presumir que Realtime substitui a persistência histórica ou a lógica de autorização.

---

## 10. Pontos de Modelagem a Resolver Antes das Migrations

| Questão para a equipe | Impacto potencial |
| :--- | :--- |
| O transporte é municipal, escolar ou multi-prefeitura? | Escopo de `Organization`, isolamento de dados e permissões. |
| O estudante pode ter mais de um endereço de embarque? | Pode exigir `StudentAddress` ou `StudentPickupPoint` em vez de endereço na solicitação. |
| Há viagens casa-escola e escola-casa? | Pode exigir direção/tipo de viagem, calendário e paradas distintas. |
| Uma viagem pode atender mais de uma escola? | Afeta `RouteStop`, `TripStudent` e regras de destino. |
| Vagas são controladas por escola, rota, turno, região ou veículo? | Pode alterar `Vacancy` e critérios de alocação. |
| Motorista pode também ser monitor? | Afeta perfis, vínculos e validação de escala. |
| Mais de um monitor por viagem é regra? | Confirma ou altera `TripMonitor`. |
| Como são definidos horários, dias letivos e calendário? | Pode exigir `Schedule`, `ServiceCalendar` ou recorrência. |
| Como tratar substituições de motorista/monitor e troca de veículo? | Pode exigir histórico de alocação em vez de apenas FKs diretas em `Trip`. |
| Qual volume e retenção esperados para GPS? | Afeta particionamento, índices e custo de armazenamento. |

---

## 11. Organização Sugerida do Projeto Django

```text
backend/
├── config/
├── apps/
│   ├── accounts/
│   ├── organizations/
│   ├── students/
│   ├── guardians/
│   ├── schools/
│   ├── vacancies/
│   ├── fleet/
│   ├── routes/
│   ├── trips/
│   ├── tracking/
│   ├── notifications/
│   └── audit/
├── requirements/
├── .env.example
└── manage.py
```

---

## 12. Sequência Recomendada de Trabalho

1. Validar o escopo, atores e processos com a equipe e representantes da operação.
2. Revisar entidades, atributos e cardinalidades deste documento.
3. Desenhar e validar o DER em ferramenta visual (`dbdiagram.io` ou `diagrams.net`).
4. Fechar regras de negócio, estados e restrições de integridade.
5. Definir estratégia multi-organização, autenticação e autorização.
6. Definir o provedor PostgreSQL, região, backups, PostGIS e estratégia de conexão.
7. Implementar o usuário customizado e os primeiros models Django.
8. Criar migrations e testes de integridade antes de construir os endpoints.
9. Implementar primeiro cadastros e vínculos; depois solicitações, frota, rotas e viagens.
10. Adicionar GPS, notificações e otimização após validar o fluxo operacional básico.

---

## 13. Critérios de Aceite da Modelagem

- Todas as entidades do escopo inicial possuem finalidade clara.
- Todos os relacionamentos possuem cardinalidade e nulabilidade definidas.
- Não há campos duplicados sem justificativa ou relacionamentos N:N sem tabela associativa.
- As regras críticas possuem estratégia de validação no banco ou backend.
- Os dados sensíveis e os limites de acesso estão identificados.
- A modelagem contempla motorista, monitor, veículo, rota, viagem e alunos transportados.
- O DER foi revisado e aprovado pela equipe antes das migrations.

---

## 14. Referências Técnicas

- Django: https://docs.djangoproject.com/
- Django REST Framework: https://www.django-rest-framework.org/
- Supabase: https://supabase.com/docs
- PostGIS no Supabase: https://supabase.com/docs/guides/database/extensions/postgis
- PostgreSQL: https://www.postgresql.org/docs/
- Google OR-Tools: https://developers.google.com/optimization
