# Projeto 11: Sistema de Gestão de Vagas e Adaptação de Transporte Escolar Inclusivo

## 1. Visão Geral e Negócios (O "Porquê")

### BRD (Business Requirements Document)
- **Objetivo do Projeto:** Módulo logístico integrado ao transporte público e escolar para otimização de rotas de vans adaptadas (com elevador hidráulico), monitorando em tempo real o embarque e desembarque de estudantes com deficiência (LBI Art. 28 e 48).
- **Problema Solucionado:** Evasão escolar causada pela falta ou ineficiência do transporte adaptado, rotas mal planejadas que causam longas horas de viagem e falta de informação em tempo real para os pais.
- **Público-Alvo:** Gestores da Frota do Transporte Escolar, Motoristas, Monitores de Van e Famílias de alunos adaptados.
- **Metas de Negócio:** Garantir 100% de acesso dos alunos com deficiência à escola, otimizar custos de combustível e tempos de rota, e eliminar o risco de esquecimento de alunos no veículo.

### Mapa de Processos (`As-Is` / `To-Be`)
- **`[As-Is: Atual]`**
  Rotas de vans adaptadas organizadas em papel → Motorista faz trajeto por intuição → Atrasos de horas e alunos perdendo aulas → Pais sem notícias do embarque/desembarque.
- **`[To-Be: Proposto]`**
  Solicitação de vaga no sistema → Otimizador logístico gera rota inteligente → Monitor marca check-in/check-out do aluno no app da van → Notificação em tempo real para pais e gestão da frota.

---

## 2. Especificação do Sistema (O "O quê")

### PRD (Product Requirements Document)

#### Funcionalidades-Chave
- Cadastro de Alunos e Necessidades Específicas de Deslocamento (tipo de cadeira, necessidade de acompanhante).
- Algoritmo Otimizador de Rotas Logísticas para Veículos Adaptados.
- App Móvel do Monitor da Van (Check-in/Check-out de alunos com confirmação visual/QR Code).
- Painel da Gestão de Frota com Telemetria e Monitoramento no Mapa em Tempo Real.

#### Histórias de Usuário (User Stories) e Critérios de Aceite

- **US01 — Confirmação de Embarque/Desembarque:**
  - *Como* Monitor da Van Escolar, *quero* dar check-in no aluno ao embarcá-lo na rampa da van, *para* notificar a família e a escola instantaneamente.
  - *Critérios de Aceite:* Clique único no nome do aluno no app do monitor; envio imediato de notificação push no app dos pais.

- **US02 — Traçado Inteligente de Rota:**
  - *Como* Gestor da Frota, *quero* que o sistema calcule a rota mais rápida considerando o endereço de 8 alunos cadeirantes, *para* reduzir o tempo de permanência no veículo.
  - *Critérios de Aceite:* Geração automática de itinerário no mapa com estimativa de horário de chegada em cada residência.

---

## 3. Personas e Cenários de Uso

### Persona 1: Seu Valdir "Valdivino" Rangel — O Motorista de Van Adaptada
- **Sociodemográfico:** 56 anos, homem cis, casado, Motorista do Transporte Escolar Inclusivo há 18 anos. CNH Categoria D com curso de Transporte Escolar. Renda: R$ 3.500,00. Letramento digital básico (utiliza WhatsApp para conversar com a esposa e aplicativo de GPS estilo Waze no celular).
- **Características Físicas e Personalidade:** Paciente, atencioso, usa boné e agasalho da frota municipal, muito cuidadoso no trânsito e querido por todas as crianças da rota.
- **Necessidades no Sistema:** Aplicativo com interface limpa, botões gigantes, rotas integradas com GPS por comando de voz e instrução clara de trânsito sem distrações visuais enquanto dirige.
- **Cenário de Uso:** Seu Valdir fixa o smartphone no painel da van, o app indica a próxima parada ("Rua das Flores, 123 - Aluno Mateus"), e o GPS guia o trajeto pela rota otimizada sem atrasos.

### Persona 2: Marisa "Mari" da Silva — A Monitora de Transporte Inclusivo
- **Sociodemográfico:** 38 anos, mulher cis, solteira, mãe de 2 filhos, Monitora de Van Escolar. Renda: R$ 2.100,00. Mora no mesmo setor da rota. Letramento digital intermediário no smartphone.
- **Características Físicas e Personalidade:** Agitada, prestativa, usa colete reflexivo e crachá, atenta à trava da rampa elevatória e ao cinto de segurança de cinco pontos das cadeiras de rodas.
- **Necessidades no Sistema:** App móvel super-rápido no celular para dar check-in no embarque do aluno e check-out na porta da escola com apenas 1 toque na tela.
- **Cenário de Uso:** Mari opera a rampa elevatória para embarcar o aluno Mateus, abre o app no celular, toca no botão verde "Mateus Embarcado" e o sistema dispara o aviso para a mãe de Mateus em tempo real.

### Persona 3: Solange "Sol" Aparecida — A Mãe Ansiosa
- **Sociodemográfico:** 32 anos, mulher cis, casada, dona de casa, mãe de um garoto de 8 anos com tetraplegia. Renda familiar: R$ 2.400,00. Letramento digital intermediário.
- **Características Físicas e Personalidade:** Cuidadosa, vive em função do bem-estar do filho, fica extremamente ansiosa durante o período em que o filho está no trajeto escolar.
- **Necessidades no Sistema:** Rastreamento do veículo em tempo real no mapa e notificações push imediatas quando o filho entra na van e quando desembarca em segurança na escola.
- **Cenário de Uso:** Sol recebe a notificação push no celular "Seu filho embarcou na Van 04 às 07:12h", abre o mapa do app e acompanha a movimentação do veículo até a chegada na escola às 07:35h, ficando totalmente tranquila.

### Persona 4: Eng. Fernando "Nando" Prado — O Gestor da Frota Municipal
- **Sociodemográfico:** 45 anos, homem cis, casado, Engenheiro de Tráfego e Gestor do Transporte Escolar da Prefeitura. Renda: R$ 10.200,00. Letramento digital avançado em softwares de GIS, logística e frotas.
- **Características Físicas e Personalidade:** Analítico, usa camisa social dobrada até os cotovelos, focado em otimização de custos, eficiência energética e pontualidade da frota.
- **Necessidades no Sistema:** Painel de controle de frota em tempo real no mapa, relatórios de consumo de combustível por quilômetro rodado e indicador de tempo médio de permanência dos alunos nos veículos.
- **Cenário de Uso:** Nando acessa o sistema na prefeitura, ajusta o algoritmo de rotas para o novo semestre e consegue reduzir em 18% a quilometragem diária das vans adaptadas sem alterar o horário de chegada dos alunos.

### Persona 5: Profa. Tássia Camargo — A Diretora Receptora na Escola
- **Sociodemográfico:** 48 anos, mulher cis, casada, Diretora de Escola Municipal Inclusiva. Renda: R$ 6.700,00. Letramento digital intermediário.
- **Características Físicas e Personalidade:** Organizada, rigorosa com horários, acompanha pessoalmente a chegada dos ônibus e vans no portão da escola todas as manhãs.
- **Necessidades no Sistema:** Visão no portal web dos horários estimados de chegada de cada van escolar na porta da escola para preparar os monitores internos de recepção.
- **Cenário de Uso:** Tássia checa o painel do portal na secretaria, observa que a Van 04 chegará em 5 minutos com 3 alunos cadeirantes e envia os estagiários de apoio para o portão principal para o desembarque.
