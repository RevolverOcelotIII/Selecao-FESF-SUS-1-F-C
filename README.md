# MedManager ERP - Plataforma de Operações Hospitalares

**MedManager** é um protótipo e Proof of Concept (PoC) de um ERP hospitalar de alta performance e customizável. O projeto demonstra uma abordagem robusta para otimizar operações hospitalares, fluxos clínicos e gestão de recursos através de uma arquitetura **Model-Driven** totalmente dinâmica.

---

## 📦 Estratégia de Entrega e Repositórios (Barema)

Para atender estritamente aos critérios de nomenclatura e organização do **Edital N.º 01/2026 (Anexo IV - Barema)**, este projeto foi replicado em múltiplos repositórios. 

**Nota Importante:** Todos os repositórios listados abaixo contêm o **exato mesmo código-fonte** da aplicação Full Stack integrada. A única diferença entre eles é o arquivo `README.md`, que foi adaptado individualmente para destacar os requisitos específicos de cada item de avaliação:

1.  **Seleção FESF-SUS – 1 F.C**: Foco na demonstração da **API Python (FastAPI)** **Interface React (Next.js)** com gerenciamento de estados utilizando **Zustand**.
2.  **Seleção FESF-SUS – 2 F.C**: Foco na orquestração **Docker** e implementação de segurança **OAuth2**.
3.  **Seleção FESF-SUS – 3 F.C**: Foco na explicação do uso de **Redis** para caches no projeto.
4.  **Seleção FESF-SUS – 4 F.C**: Foco na suíte de testes automatizados (**Unit & Integration Tests**).
---
### Descrição de requisitos do Seleção FESF-SUS – 1 F.C:

**1. Desenvolvimento Funcional de API (Python, FastAPI e SQLAlchemy)**

  A API do MedManager utiliza uma arquitetura baseada em Camada de Serviço (Service Layer), separando as rotas (Controllers) da lógica de negócio
  e da persistência de dados.

   * Ponto de Entrada e Roteamento (FastAPI):
       * O arquivo `backend/app/main.py` centraliza a aplicação, configurando middlewares de CORS e incluindo os roteadores de todos os módulos
         (Attendances, Patients, Procedures, etc.).
       * Exemplo de Controller: `backend/app/controllers/attendance_procedures.py` gerencia os endpoints clínicos com injeção de dependência para
         autenticação e banco de dados.

   * Persistência e Modelagem (SQLAlchemy):
       * Configuração: `backend/app/core/database.py` define a conexão com o PostgreSQL e a sessão do ORM.
       * Entidades: Os modelos em `backend/app/models/` utilizam o SQLAlchemy 2.0. Destaco o `backend/app/models/catalog.py` (Procedimentos e
         Medicamentos) e `backend/app/models/attendance_procedure.py`, que gerenciam relacionamentos complexos de "muitos-para-muitos" e chaves
         estrangeiras dinâmicas.
       * Migrações: O uso de Alembic (diretório `backend/alembic/`) comprova o controle de versão do esquema do banco de dados.

   * Lógica de Negócio e Segurança:
       * A pasta `backend/app/services/` (ex: patients.py, employees.py) encapsula as regras de validação e filtros de visibilidade por papel
         (RBAC), garantindo que a API não apenas entregue dados, mas proteja a integridade institucional.

  ---

 **2. Interface Frontend (React, Next.js e Zustand)**

  O frontend foi desenvolvido utilizando as práticas mais modernas do ecossistema React, focado em produtividade e consistência visual.

   * Framework e Estrutura (Next.js 15):
       * Utiliza o App Router (`frontend/src/app/`), aproveitando componentes de servidor para metadados e componentes de cliente para
         interatividade.
       * O layout global em `frontend/src/app/layout.tsx` e `LayoutClient.tsx` coordena a estrutura da página, iconografia e temas.

   * Gestão de Estado Global (Zustand):
       * Centralização: O arquivo `frontend/src/store/useAuthStore.ts` é o coração da gestão de estado. Ele armazena o usuário autenticado, o
         status de carregamento e as permissões de acesso (RBAC).
       * Reatividade: Através da AuthGuard (`frontend/src/components/layout/AuthGuard.tsx`), o sistema reage em tempo real a mudanças no estado do
         Zustand (ex: redirecionando para /login se a sessão expirar ou for invalidada no Redis).

   * Desenvolvimento Funcional e Interface:
       * O sistema é Model-Driven. Componentes genéricos como `Grid.tsx` e `FormModal.tsx` em `frontend/src/components/layout/` renderizam interfaces
         complexas dinamicamente baseadas em configurações (ex: frontend/src/models/patient.ts).
       * Isso comprova uma interface altamente funcional que minimiza código duplicado e facilita a manutenção.

## 🏗 Visão Geral: Lógica Dinâmica e Configuração

Diferente de ERPs rígidos, o MedManager utiliza um motor de **entidades dinâmicas**. O comportamento do fluxo clínico não é definido por código fixo, mas pela parametrização administrativa:

1.  **Dynamic Roles**: O administrador cria cargos (ex: "Cirurgião", "Enfermeiro de Triagem") e os vincula a um dos **5 Níveis de Acesso Base** (Medical, Logistics, Pharmaceutical, Admin ou Logged User).
2.  **Dynamic Procedures**: Cada ação clínica é um item de catálogo onde o administrador define a **Permission Matrix (RBAC)**:
    *   **Dispatch Roles**: Quais cargos podem *solicitar* o procedimento.
    *   **Execute Roles**: Quais cargos podem *executar* e registrar notas clínicas.

---

## 🛠 Tech Stack

### Frontend
- **Framework:** Next.js 15+ (App Router) | TypeScript
- **State Management:** Zustand (Session Global, RBAC e UI State)
- **Internationalization:** i18n-js (Suporte a PT-BR e EN)
- **Testing:** Jest + React Testing Library (Política de **Zero-Mock** para componentes internos)
- **Styling:** Vanilla CSS Variables (Paleta Zinc, cores OKLCH)

### Backend
- **Framework:** FastAPI (Python 3.11+)
- **Database:** PostgreSQL | SQLAlchemy 2.0 (ORM) | Alembic (Migrations)
- **Cache:** Redis (Camada de cache para performance de queries e catálogos)
- **Architecture:** **Service Layer Pattern** para desacoplamento da lógica de negócio.

---

## 🚀 Passo a Passo do Sistema (Tutorial)

> **Nota**: Para testar o fluxo completo, é necessário criar pelo menos **três usuários** com perfis diferentes (ex: Atendente, Enfermeiro e Médico).

### 1. Parametrização das Regras de Negócio (Admin Setup)
Logue como **Admin** para configurar a infraestrutura lógica:

*   **Criar Roles**: Em **Administration > Roles**, crie os cargos "Recepcionista" (Level: Attendant), "Enfermeiro" (Level: Nurse) e "Médico" (Level: Doctor).
<img width="1912" height="796" alt="Captura de tela 2026-05-27 211017" src="https://github.com/user-attachments/assets/a1a1b7cc-c5d0-4a52-872e-16e6b6584d18" />

*   **Registrar Staff**: Em **Administration > Employees**, cadastre os profissionais.
<img width="1913" height="843" alt="Captura de tela 2026-05-27 211134" src="https://github.com/user-attachments/assets/a7925b92-ab0f-4d22-bc95-ac9d4a2790cb" />

*   **Vincular Contas**: Em **Administration > Users**, crie as credenciais de login para os funcionários.
<img width="1918" height="775" alt="Captura de tela 2026-05-27 211448" src="https://github.com/user-attachments/assets/f6806df2-7c4f-4a7a-9c32-cac1f679c361" />

*   **Configurar Procedimentos**: Em **Administration > Procedures**, defina as regras de Dispatch e Execute para procedimentos como "Triagem" e "Consulta".
<img width="677" height="830" alt="Captura de tela 2026-05-27 211222" src="https://github.com/user-attachments/assets/9431166a-83a6-45f2-a481-1cb998957874" />

### 2. Fluxo de Admissão (Attendant Flow)
Logue como o **Recepcionista**:

*   **Registrar Paciente**: Em **Workspace > Patients**, faça o cadastro inicial.
<img width="1918" height="907" alt="Captura de tela 2026-05-27 211625" src="https://github.com/user-attachments/assets/13f8aa5a-7bee-4ed8-9d25-d2b2e0451ad7" />

*   **Iniciar Attendance**: Crie o atendimento e solicite a "Triagem". O sistema filtrará automaticamente apenas enfermeiros aptos.

### 3. Execução Clínica e Encaminhamento (Nurse Flow)
Logue como o **Enfermeiro**:

*   **Realizar Triagem**: Altere o status para **"In Progress"**, registre os sinais vitais e finalize como **"Done"**.
*   **Workflow em Cadeia**: Antes de fechar, solicite uma **"Consulta"**. Por ser um Enfermeiro, você tem autoridade para encaminhar o paciente ao Médico.
<img width="1918" height="912" alt="Captura de tela 2026-05-27 211654" src="https://github.com/user-attachments/assets/5685b1ad-34f3-43c4-bc9a-e2f81b223f09" />

### 4. Atendimento Especializado (Doctor Flow)
Logue como o **Médico**:

*   **Medical Review**: Revise as notas da triagem e execute a "Consulta", adicionando diagnóstico e tratamento (com busca integrada de **Medicines**).

---

## 🐳 Setup e Instalação (Docker)

### 1. Pré-requisitos
- Docker & Docker Compose.
- Arquivo `.env` na raiz (baseie-se no `.env.example`).

### 2. Subir a Infraestrutura
```bash
# Inicialização de todos os serviços (API, Web, DB, Redis)
docker-compose up --build
```

### 3. Migrações e Dados Iniciais (Seed)
As migrations rodam automaticamente. Para popular o banco com a conta Admin inicial e roles base, execute:

```bash
# Popular o banco (Seed)
docker-compose exec api python -m app.seed
```

**Credenciais Administrativas Iniciais:**
- **Email**: `admin@medmanager.com`
- **Senha**: `admin123`

### 4. Executando Testes
**Backend Tests (Pytest):**
```bash
docker-compose exec api pytest app/tests/
```

**Frontend Tests (Jest):**
```bash
docker-compose exec frontend npm test
```

---

## 🌍 Suporte a Idiomas (i18n)
O sistema é totalmente internacionalizado. Preferências de idioma são persistidas no `localStorage`.
- 🇧🇷 **Português (Padrão)**
- 🇺🇸 **Inglês**

---
*Este projeto foi desenvolvido como demonstração técnica de nível Pleno para o Processo Seletivo FESF-SUS (2026).*
