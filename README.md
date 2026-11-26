# 🚌 Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo

Sistema desenvolvido como Projeto Extensionista para organizar o cadastro de estudantes e responsáveis, gestão de vagas escolares, adaptação de frota acessível (veículos com elevador hidráulico e necessidades específicas de deslocamento), planejamento de rotas, execução de viagens, check-in/check-out pelo aplicativo do monitor, monitoramento em mapa e notificações às famílias.

---

## 🎯 Objetivos do Projeto

- **Gestão de Vagas e Solicitações:** Organizar pedidos de transporte escolar inclusivo, alocação de vagas e vínculos ativos (`Enrollment`).
- **Planejamento Logístico:** Estruturar rotas e paradas considerando endereços, horários e características de acessibilidade dos veículos.
- **Operação em Tempo Real:** Registrar embarque e desembarque (`BOARDING` / `ALIGHTING`) com confirmação operacional pelo monitor.
- **Comunicação com Famílias:** Notificar responsáveis autorizados sobre eventos relevantes da viagem.
- **Monitoramento da Frota:** Acompanhar localização dos veículos em mapa e apoiar a gestão operacional com auditoria.

---

## 👥 Equipe e Divisão de Papéis

| Área | Integrantes | Responsabilidades Principais |
| :--- | :--- | :--- |
| **Frontend** | Eduardo, Erick | Interface web (painel de gestão e portal), experiência do usuário, acessibilidade, consumo da API e visualização em mapas |
| **Backend** | Rita, Erick | API REST, regras de negócio, validações, permissões, integrações, notificações e serviços de roteamento |
| **Banco de Dados e Modelagem** | Edmilson, Karina, Eduardo | Modelagem conceitual/lógica (DER), dicionário de dados, integridade relacional, PostGIS e segurança/LGPD |

---

## 🏗️ Arquitetura e Stack Tecnológica

A arquitetura adota um **monólito modular no backend** (concentrando regras de negócio e operações sensíveis na API) combinado a um **frontend moderno tipado** e banco relacional com suporte geoespacial.

### Frontend
- **Next.js (App Router)** + **React** + **TypeScript**
- **Tailwind CSS** + **shadcn/ui** (componentes acessíveis baseados em Radix UI, com foco em acessibilidade e navegação por teclado)
- **TanStack Query (React Query)** para cache, sincronização e estado de requisições
- **React Hook Form + Zod** para formulários e validação com tipagem compartilhada
- **Leaflet (`react-leaflet`) / Mapbox** para visualização de rotas, paradas e rastreamento da frota em mapa
- **Lucide Icons** para iconografia consistente
- **PWA / Mobile-first** para o fluxo do monitor (check-in/check-out em campo com suporte a leitura de QR Code)
- **ESLint + Prettier** para padronização e qualidade de código

### Backend
- **Python** + **Django** + **Django REST Framework (DRF)**
- **Django Channels** ou **Supabase Realtime** *(em avaliação para eventos e localização em tempo real)*
- **Google OR-Tools** *(em avaliação para otimização de itinerários e rotas)*
- **Firebase Cloud Messaging (FCM)** *(proposta para notificações push)*

### Banco de Dados e Infraestrutura
- **PostgreSQL** gerenciado pelo **Supabase**
- **PostGIS** para consultas espaciais, coordenadas (`SRID 4326`), distâncias e pontos de parada
- **Supabase Storage** *(opcional, com buckets privados e URLs assinadas para documentos)*

---

## 🧩 Escopo Funcional e Domínios

| Domínio | Entidades Principais | Responsabilidade |
| :--- | :--- | :--- |
| **Administração e Acesso** | `Organization`, `User`, `OrganizationMembership` | Organizações/prefeituras, autenticação customizada e perfis de acesso |
| **Alunos e Responsáveis** | `Student`, `Guardian`, `StudentGuardian`, `StudentNeed` | Cadastro de estudantes, múltiplos responsáveis e necessidades de deslocamento |
| **Escolas e Vagas** | `School`, `Vacancy`, `TransportRequest`, `Enrollment` | Instituições, capacidade total/adaptada, solicitações e vínculos aprovados |
| **Frota** | `Vehicle`, `Driver`, `Monitor` | Veículos (capacidade, cadeiras, elevador hidráulico), motoristas e monitores |
| **Logística** | `Route`, `RouteStop`, `Trip`, `TripMonitor`, `TripStudent` | Itinerários, paradas ordenadas, execução de viagens e alocação de alunos |
| **Operação e Monitoramento** | `AttendanceEvent`, `VehicleLocation`, `Notification`, `AuditLog` | Embarque/desembarque, histórico GPS, notificações e trilha de auditoria |

---

## 🔒 Segurança, Privacidade e LGPD

Por tratar **dados pessoais de crianças** e **informações sensíveis relacionadas à saúde/deficiência e necessidades de deslocamento**, o projeto prevê desde a concepção:

- **Menor privilégio e isolamento por organização:** Verificação de escopo organizacional e perfil em todas as operações da API.
- **Restrição de dados sensíveis:** `StudentNeed` e dados de localização são expostos apenas a perfis com necessidade operacional estrita.
- **Proteção de credenciais:** Nenhuma chave privilegiada do banco/Supabase é exposta no frontend; acesso a dados passa exclusivamente pela API Django.
- **Auditoria e retenção:** Registro de ações críticas em `AuditLog` (sem armazenar conteúdo sensível em logs) e definição de política de retenção para histórico de GPS.

---

## 📁 Estrutura Planejada do Projeto

```text
.
├── backend/                  # API modular em Django + DRF
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
├── frontend/                 # Aplicação Next.js + TypeScript + Tailwind (em estruturação)
└── README.md
```

---

## 🗺️ Roadmap Inicial

- [x] Definição de escopo, objetivos de negócio e papéis da equipe
- [x] Modelagem inicial de dados (v0.1) e arquitetura de referência
- [ ] Validação do DER (Diagrama Entidade-Relacionamento) e regras de negócio com a equipe
- [ ] Configuração do ambiente PostgreSQL + PostGIS e usuário customizado no Django
- [ ] Implementação dos módulos base (cadastros, vínculos, escolas, vagas e frota)
- [ ] Desenvolvimento do painel administrativo e fluxo de solicitações no Frontend
- [ ] Implementação de viagens, check-in/check-out do monitor, mapa em tempo real e notificações

---

## 📚 Referências Técnicas

- [Django Documentation](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Next.js Documentation](https://nextjs.org/docs)
- [Supabase & PostGIS](https://supabase.com/docs/guides/database/extensions/postgis)
- [Google OR-Tools](https://developers.google.com/optimization)
