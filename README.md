# Documentação de Projeto — FitControl  
**Versão 1.0**  
Projeto elaborado pelo(s) aluno(s) AAAA  
Disciplina: Projeto de Software  
**Data:** 10/11/2025  

---

## 📌 Tabela de Conteúdo
1. [Introdução](#introdução)
2. [Modelos de Usuário e Requisitos](#modelos-de-usuário-e-requisitos)
   - [Descrição de Atores](#descrição-de-atores)
   - [Modelo de Casos de Uso](#modelo-de-casos-de-uso)
   - [Diagramas de Sequência e Contratos](#diagramas-de-sequência-e-contratos)
3. [Modelos de Projeto](#modelos-de-projeto)
   - [Arquitetura](#arquitetura)
   - [Diagrama de Componentes e Implantação](#diagrama-de-componentes-e-implantação)
   - [Diagrama de Classes](#diagrama-de-classes)
   - [Diagramas de Sequência](#diagramas-de-sequência)
   - [Diagramas de Comunicação](#diagramas-de-comunicação)
   - [Diagramas de Estados](#diagramas-de-estados)
4. [Modelos de Dados](#modelos-de-dados)

---

## 📌 Histórico de Revisões
| Nome | Data | Razões para Mudança | Versão |
|------|------|----------------------|---------|
| Diogo Caribe Brunoro | 10/11/2025 | Realização da documentação do projeto | 1.0.0 |

---

# Introdução
Este documento apresenta:
1. A elaboração e revisão de modelos de domínio.  
2. Os modelos de projeto do sistema **FitControl**.  

O documento de especificação do sistema serve como referência principal para o entendimento do domínio e requisitos.

---

# Modelos de Usuário e Requisitos

## Descrição de Atores

### 👤 Cliente
O Cliente é o usuário final da academia que utiliza a plataforma para visualizar e gerenciar suas fichas de treino. Pode salvar fichas, acompanhar treinos e registrar sua evolução.

### 🏋️ Personal Trainer
Profissional responsável por criar, publicar e gerenciar fichas de treino. Define exercícios, séries e cargas, ajusta treinos e acompanha o desempenho dos clientes.

### 🏢 Academia
Entidade administradora do sistema. Gerencia cadastros, permissões de acesso e é capaz de gerar relatórios sobre uso e evolução dos clientes.

---

## Modelo de Casos de Uso

<img width="458" height="674" alt="image" src="https://github.com/user-attachments/assets/bd547229-dc88-41e0-8491-2ec75d3e9db1" />


| ID | Caso de Uso | Ator Principal | Descrição |
|----|--------------|----------------|-----------|
| UC-01 | Cadastrar Cliente | Academia | Cadastra novos clientes. |
| UC-02 | Cadastrar Personal Trainer | Academia | Cadastra personal trainers. |
| UC-03 | Criar Ficha de Treino | Personal Trainer | Cria ficha de treino para um cliente. |
| UC-04 | Editar Ficha de Treino | Personal Trainer | Edita ficha existente. |
| UC-05 | Visualizar Fichas Disponíveis | Cliente | Visualiza fichas disponíveis. |
| UC-06 | Salvar Ficha de Treino | Cliente | Salva ficha de sua preferência. |
| UC-07 | Registrar Evolução | Cliente | Registra evolução em ficha salva. |
| UC-08 | Gerenciar Permissões | Academia | Configura permissões de acesso. |
| UC-09 | Visualizar Relatórios | Academia | Visualiza relatórios do sistema. |

---

# Diagramas de Sequência e Contratos

## 📄 Contrato — Criar Ficha de Treino
**Operação:** `criarFicha(clienteID, exercicios, series, cargas)`  
**Caso de Uso Relacionado:** UC-03

### Pré-condições
1. Personal Trainer autenticado.  
2. Cliente cadastrado.  

### Pós-condições
1. Uma nova ficha é criada e associada ao cliente.  
2. A ficha é salva no banco de dados.  
3. Retorno de confirmação ao Personal Trainer.  

---

## 📄 Contrato — Salvar Ficha de Treino
**Operação:** `salvarFicha(fichaID)`  
**Caso de Uso Relacionado:** UC-06

### Pré-condições
1. Cliente autenticado.  
2. A ficha deve existir e estar disponível.  

### Pós-condições
1. A ficha é associada ao cliente.  
2. O sistema confirma o salvamento.  

---

## 📄 Contrato — Registrar Evolução
**Operação:** `registrarEvolucao(fichaID, data, progresso)`  
**Caso de Uso Relacionado:** UC-07

### Pré-condições
1. Cliente autenticado.  
2. A ficha deve estar salva pelo cliente.  

### Pós-condições
1. O progresso é registrado na ficha.  
2. Histórico de evolução atualizado.  
3. Retorno de confirmação ao cliente.  

---

# Modelos de Projeto

## Arquitetura

### Frontend
- SPA em React  
- Arquitetura com Atoms (átomos, moléculas, organismos)  
- Comunicação via API REST  
- Autenticação JWT  

### Backend
- Clean Architecture  
- Camadas organizadas em: Entidades → Casos de Uso → Adaptadores → Frameworks  
- API RESTful  
- Regras de negócio isoladas  

### Banco de Dados
- PostgreSQL  
- Estrutura relacional  
- Foco em integridade e consultas eficientes  

### Segurança e Escalabilidade
- HTTPS  
- JWT  
- Frontend e backend desacoplados  
- Fácil manutenção e expansão  

---

## Diagrama de Componentes e Implantação
<img width="492" height="214" alt="image" src="https://github.com/user-attachments/assets/e7c32547-78b6-47e7-8f31-88c7c2c3f71f" />

---

## Diagrama de Classes
<img width="454" height="522" alt="image" src="https://github.com/user-attachments/assets/5b59b673-7bd2-472e-8615-3cff9fd810ab" />

---

## Diagramas de Sequência
<img width="452" height="191" alt="image" src="https://github.com/user-attachments/assets/46284bab-c479-4653-b901-2bdd6e0d0520" />

<img width="446" height="188" alt="image" src="https://github.com/user-attachments/assets/57517c02-4a28-4a89-8014-1f2a3a600bf2" />

<img width="449" height="184" alt="image" src="https://github.com/user-attachments/assets/bc099c61-3028-47ee-a510-66c7ec986cc0" />

---

## Diagramas de Comunicação
<img width="501" height="72" alt="image" src="https://github.com/user-attachments/assets/b0c56d90-2c30-45f3-a324-cb12adab0793" />

<img width="498" height="76" alt="image" src="https://github.com/user-attachments/assets/a02c196f-8261-4cf4-94e6-f2db8a421675" />

<img width="496" height="73" alt="image" src="https://github.com/user-attachments/assets/02e14ced-4fc3-446b-bd74-382748242de5" />

---

## Diagramas de Estados
<img width="451" height="316" alt="image" src="https://github.com/user-attachments/assets/a900c850-e8cf-4174-a803-1027fb5b2152" />

---

# Modelos de Dados
<img width="534" height="238" alt="image" src="https://github.com/user-attachments/assets/2f575e68-20ee-4520-a4d8-b6fc346626f7" />

