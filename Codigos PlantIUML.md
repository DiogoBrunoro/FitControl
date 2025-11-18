# Diagrama de Casos de Uso

```plantuml
@startuml
left to right direction

' Declara os atores fora da fronteira do sistema
actor "Cliente" as Cliente
actor "Personal Trainer" as PT
actor "Academia" as Academia

' Fronteira do sistema
package "FitControl" {

    package "Casos de Uso do Cliente" {
        usecase UC05 as "Visualizar Fichas Disponíveis"
        usecase UC06 as "Salvar Ficha de Treino"
        usecase UC07 as "Registrar Evolução"

        UC06 .> UC05 : <<include>>
        UC07 .> UC06 : <<include>>
    }

    package "Casos de Uso do Personal Trainer" {
        usecase UC03 as "Criar Ficha de Treino"
        usecase UC04 as "Editar Ficha de Treino"

        UC04 .> UC03 : <<extend>>
    }

    package "Casos de Uso da Academia" {
        usecase UC01 as "Cadastrar Cliente"
        usecase UC02 as "Cadastrar Personal Trainer"
        usecase UC08 as "Gerenciar Permissões de Usuários"
        usecase UC09 as "Visualizar Relatórios do Sistema"
    }
}

' Relacionamentos ator -> casos de uso
Cliente --> UC05
Cliente --> UC06
Cliente --> UC07

PT --> UC03
PT --> UC04

Academia --> UC01
Academia --> UC02
Academia --> UC08
Academia --> UC09
@enduml

```

---


# Diagrama de Componentes e Implantação

```plantuml
@startuml
title Arquitetura Combinada e Colorida - FitControl (Services Especializados)
skinparam componentStyle rectangle
skinparam nodeStyle rectangle
skinparam packageStyle rectangle
skinparam backgroundColor #FAFAFA
skinparam shadowing false
left to right direction

' ======= NÓ DO CLIENTE =======
node "Cliente (Navegador / Aplicativo Web)" as Cliente #E8F0FE {
    package "Frontend (SPA - React)" #D2E3FC {
        [Atoms] <<component>> #E8F0FE
        [Molecules] <<component>> #E8F0FE
        [Organisms] <<component>> #E8F0FE
        [Pages] <<component>> #E8F0FE

        [Pages] --> [Organisms]
        [Organisms] --> [Molecules]
        [Molecules] --> [Atoms]
    }
}

' ======= NÓ DO SERVIDOR =======
node "Servidor de Aplicação (Backend)" as Servidor #E6F4EA {
    package "Backend (Clean Architecture)" #C8E6C9 {

        [Controllers/API REST] <<component>> #E6F4EA

        package "Services" #A5D6A7 {
            [ServiceAcademia] <<component>> #C8E6C9
            [ServicePersonal] <<component>> #C8E6C9
            [ServiceCliente] <<component>> #C8E6C9
        }

        [Use Cases] <<component>> #E6F4EA
        [Entities] <<component>> #E6F4EA
        [Repositories/DAO] <<component>> #E6F4EA

        ' ======= RELAÇÕES INTERNAS =======
        [Controllers/API REST] --> [ServiceAcademia]
        [Controllers/API REST] --> [ServicePersonal]
        [Controllers/API REST] --> [ServiceCliente]

        [ServiceAcademia] --> [Use Cases]
        [ServicePersonal] --> [Use Cases]
        [ServiceCliente] --> [Use Cases]

        [Use Cases] --> [Entities]
        [Use Cases] --> [Repositories/DAO]
    }
}

' ======= NÓ DO BANCO DE DADOS =======
node "Servidor de Banco de Dados" as DB #FFF4E5 {
    database "PostgreSQL" as Banco #FFE0B2
}

' ======= CONEXÕES ENTRE OS NÓS =======
Cliente -[#0000FF]-> Servidor : HTTPS / API REST (JSON)
Servidor -[#008000]-> DB : SQL / JDBC

' ======= CONEXÕES ENTRE CAMADAS =======
[Pages] --> [Controllers/API REST] : Requisições JSON
[Repositories/DAO] --> Banco : Operações CRUD

legend right
|Cor|Módulo|
|--|--|
|#D2E3FC|Frontend (React + Atoms Architecture)|
|#C8E6C9|Backend (Clean Architecture + Services Especializados)|
|#FFE0B2|Banco de Dados (PostgreSQL)|
endlegend
@enduml
```



---

# Diagrama de Classe 

```plantuml

@startuml
title Diagrama de Classes - FitControl
skinparam classAttributeIconSize 0
skinparam backgroundColor #FAFAFA
skinparam class {
    BackgroundColor<<Entity>> #E6F4EA
    BackgroundColor<<Interface>> #D2E3FC
    BackgroundColor<<Enum>> #FFE0B2
    BorderColor #666666
}

' ========================
' ======= ENUMS ==========
' ========================
enum TipoUsuario <<Enum>> {
    CLIENTE
    PERSONAL
    ADMIN
}

enum NivelTreino <<Enum>> {
    INICIANTE
    INTERMEDIARIO
    AVANCADO
}

enum StatusFicha <<Enum>> {
    ATIVA
    INATIVA
    CONCLUIDA
}

' ========================
' ======= CLASSES =========
' ========================
class Academia <<Entity>> {
    - id: int
    - nome: string
    - cnpj: string
    - endereco: string
    + cadastrarCliente()
    + cadastrarPersonal()
    + gerarRelatorios()
}

class Cliente <<Entity>> {
    - id: int
    - nome: string
    - email: string
    - senha: string
    - dataNascimento: Date
    - nivelTreino: NivelTreino
    + visualizarFichas()
    + salvarFicha()
    + registrarEvolucao()
}

class PersonalTrainer <<Entity>> {
    - id: int
    - nome: string
    - email: string
    - especialidade: string
    + criarFichaTreino()
    + editarFichaTreino()
}

class FichaTreino <<Entity>> {
    - id: int
    - titulo: string
    - descricao: string
    - status: StatusFicha
    - dataCriacao: Date
    + adicionarExercicio()
    + concluirFicha()
}

class Exercicio <<Entity>> {
    - id: int
    - nome: string
    - series: int
    - repeticoes: int
    - carga: double
    + ajustarCarga()
}

class Evolucao <<Entity>> {
    - id: int
    - dataRegistro: Date
    - pesoAtual: double
    - percentualGordura: double
    - observacoes: string
}

' ==========================
' ======= INTERFACES =======
' ==========================
interface IRepository <<Interface>> {
    + salvar(objeto)
    + atualizar(objeto)
    + deletar(id)
    + buscarPorId(id)
}

interface IAuthService <<Interface>> {
    + autenticar(email, senha)
    + gerarToken(usuario)
    + validarToken(token)
}

interface IRelatorioService <<Interface>> {
    + gerarRelatorioMensal(academiaId)
    + gerarRelatorioEvolucao(clienteId)
}

' ==========================
' ======= RELAÇÕES =========
' ==========================

' Academia gerencia Clientes e Personais
Academia "1" -- "0..*" Cliente : gerencia >
Academia "1" -- "0..*" PersonalTrainer : < administra

' Personal cria Fichas
PersonalTrainer "1" -- "0..*" FichaTreino : cria >

' Cliente possui fichas (salvas ou ativas)
Cliente "1" -- "0..*" FichaTreino : possui >

' Ficha contém exercícios
FichaTreino "1" -- "1..*" Exercicio : contém >

' Cliente tem histórico de evolução
Cliente "1" -- "0..*" Evolucao : registra >

' Repositórios e serviços implementados por classes específicas
IRepository <|.. Academia
IRepository <|.. Cliente
IRepository <|.. PersonalTrainer
IRepository <|.. FichaTreino
IRepository <|.. Exercicio
IRepository <|.. Evolucao

IAuthService <|.. PersonalTrainer
IAuthService <|.. Cliente
IRelatorioService <|.. Academia

' ==========================
' ======= NOTAS ============
' ==========================
note top of Cliente
Representa o usuário final do sistema,
que pode visualizar, salvar fichas e registrar evolução.
end note

note right of FichaTreino
Cada ficha é criada por um personal e associada a um cliente.
Contém uma lista de exercícios configurados.
end note

@enduml
```



---

# Diagrama de Sequência


## Diagrama de Sequência – Criar Ficha de Treino

```plantuml
@startuml
title UC-03: Criar Ficha de Treino
skinparam backgroundColor #F9FAFB
skinparam sequence {
    ArrowColor #444444
    ActorBorderColor #004D40
    ActorBackgroundColor #A7FFEB
    ParticipantBorderColor #1E88E5
    ParticipantBackgroundColor #BBDEFB
    LifeLineBorderColor #666666
    LifeLineBackgroundColor #E3F2FD
    ActivationBarColor #1976D2
    SequenceBoxBorderColor #0D47A1
}

actor "Personal Trainer" as PT
participant "UI - Web/App" as UI
participant "Controller" as Ctrl
participant "Service de FichaTreino" as Svc
participant "Repository de FichaTreino" as Repo
database "Banco de Dados" as DB

== Criação da Ficha ==
PT -> UI : criarFicha(clienteID, exercicios, series, cargas)
activate UI

UI -> Ctrl : enviarDadosFicha()
activate Ctrl

Ctrl -> Svc : validarDadosFicha()
activate Svc

Svc -> Repo : salvarFicha(clienteID, exercicios)
activate Repo

Repo -> DB : INSERT ficha_treino
DB --> Repo : Confirmação OK
deactivate Repo

Svc --> Ctrl : fichaCriada
deactivate Svc

Ctrl --> UI : sucesso("Ficha criada com sucesso")
deactivate Ctrl

UI --> PT : mensagem de confirmação
deactivate UI
@enduml
```




##Diagrama de Sequência – Salvar Ficha de Treino

```plantuml
@startuml
title UC-06: Salvar Ficha de Treino
skinparam backgroundColor #FDFBF9
skinparam sequence {
    ArrowColor #444444
    ActorBorderColor #1565C0
    ActorBackgroundColor #BBDEFB
    ParticipantBorderColor #43A047
    ParticipantBackgroundColor #C8E6C9
    LifeLineBorderColor #666666
    LifeLineBackgroundColor #E8F5E9
    ActivationBarColor #2E7D32
}

actor "Cliente" as Cliente
participant "App Mobile" as UI
participant "Controller" as Ctrl
participant "Service de Cliente" as Svc
participant "Repository de FichaTreino" as Repo
database "Banco de Dados" as DB

== Salvando Ficha ==
Cliente -> UI : clicar "Salvar Ficha"
activate UI

UI -> Ctrl : requisitarSalvarFicha(fichaID)
activate Ctrl

Ctrl -> Svc : validarAutenticacao(cliente)
activate Svc

Svc -> Repo : vincularFichaAoCliente(fichaID, clienteID)
activate Repo

Repo -> DB : UPDATE ficha_treino SET clienteID
DB --> Repo : Confirmação OK
deactivate Repo

Svc --> Ctrl : fichaSalva = true
deactivate Svc

Ctrl --> UI : mensagem("Ficha salva com sucesso")
deactivate Ctrl

UI --> Cliente : confirmação visual
deactivate UI
@enduml
```




## Diagrama de Sequência – Registrar Evolução


```plantuml
@startuml
title UC-07: Registrar Evolução
skinparam backgroundColor #F8FBF9
skinparam sequence {
    ArrowColor #444444
    ActorBorderColor #880E4F
    ActorBackgroundColor #F8BBD0
    ParticipantBorderColor #6A1B9A
    ParticipantBackgroundColor #E1BEE7
    LifeLineBorderColor #666666
    LifeLineBackgroundColor #F3E5F5
    ActivationBarColor #8E24AA
}

actor "Cliente" as Cliente
participant "App Mobile" as UI
participant "Controller" as Ctrl
participant "Service de Evolução" as Svc
participant "Repository de Evolução" as Repo
database "Banco de Dados" as DB

== Registro de Evolução ==
Cliente -> UI : registrarEvolucao(fichaID, data, progresso)
activate UI

UI -> Ctrl : enviarDadosEvolucao()
activate Ctrl

Ctrl -> Svc : validarFicha(fichaID)
activate Svc

Svc -> Repo : salvarEvolucao(clienteID, fichaID, progresso)
activate Repo

Repo -> DB : INSERT evolucao
DB --> Repo : confirmação OK
deactivate Repo

Svc --> Ctrl : sucesso("Evolução registrada")
deactivate Svc

Ctrl --> UI : resposta positiva
deactivate Ctrl

UI --> Cliente : mensagem "Evolução registrada com sucesso"
deactivate UI
@enduml
```



---

# Diagrama de Sequência

## Diagrama de Sequência Criar Ficha de Treino

```plantuml
@startuml
title UC-03 - Criar Ficha de Treino (Diagrama de Comunicação)
skinparam backgroundColor #FAFAFA
skinparam componentStyle rectangle
skinparam object {
    BackgroundColor<<Ator>> #A7FFEB
    BackgroundColor<<Interface>> #BBDEFB
    BackgroundColor<<Service>> #C8E6C9
    BackgroundColor<<Repository>> #FFE0B2
    BackgroundColor<<Database>> #F3E5F5
    BorderColor #555555
}

' === Objetos (clientes e fornecedores) ===
object "PersonalTrainer\n(cliente)" as PT <<Ator>>
object "UI - Web/App\n(cliente/fornecedor)" as UI <<Interface>>
object "Controller\n(cliente/fornecedor)" as Ctrl <<Interface>>
object "ServiceFichaTreino\n(cliente/fornecedor)" as Svc <<Service>>
object "RepoFichaTreino\n(fornecedor)" as Repo <<Repository>>
object "Banco de Dados\n(fornecedor)" as DB <<Database>>

' === Layout vertical compacto ===
PT -down- UI
UI -down- Ctrl
Ctrl -down- Svc
Svc -down- Repo
Repo -down- DB

' === Links (relações) ===
PT -- UI : vínculo
UI -- Ctrl : vínculo
Ctrl -- Svc : vínculo
Svc -- Repo : vínculo
Repo -- DB : vínculo

' === Mensagens numeradas ===
PT -> UI : 1: criarFicha(clienteID, exercicios, series, cargas)
UI -> Ctrl : 1.1: enviarDadosFicha()
Ctrl -> Svc : 1.2: validarDadosFicha()
Svc -> Repo : 1.3: salvarFicha(clienteID, exercicios)
Repo -> DB : 1.3.1: INSERT ficha_treino
DB --> Repo : 1.3.2: confirmação OK
Repo --> Svc : 1.4: retorno fichaCriada
Svc --> Ctrl : 1.5: sucesso("Ficha criada")
Ctrl --> UI : 1.6: resposta("OK")
UI --> PT : 1.7: mensagem de confirmação
@enduml
```


## Diagrama de Sequência Salvar Ficha de Treino

```plantuml
@startuml
title UC-06 - Salvar Ficha de Treino (Diagrama de Comunicação)
skinparam backgroundColor #FEFEFC
skinparam componentStyle rectangle
skinparam object {
    BackgroundColor<<Ator>> #BBDEFB
    BackgroundColor<<Interface>> #C8E6C9
    BackgroundColor<<Service>> #FFF59D
    BackgroundColor<<Repository>> #FFCC80
    BackgroundColor<<Database>> #E1BEE7
    BorderColor #444444
}

' === Objetos ===
object "Cliente\n(cliente)" as Cliente <<Ator>>
object "App Mobile\n(cliente/fornecedor)" as UI <<Interface>>
object "Controller\n(cliente/fornecedor)" as Ctrl <<Interface>>
object "ServiceCliente\n(cliente/fornecedor)" as Svc <<Service>>
object "RepoFichaTreino\n(fornecedor)" as Repo <<Repository>>
object "Banco de Dados\n(fornecedor)" as DB <<Database>>

' === Layout vertical ===
Cliente -down- UI
UI -down- Ctrl
Ctrl -down- Svc
Svc -down- Repo
Repo -down- DB

' === Links ===
Cliente -- UI
UI -- Ctrl
Ctrl -- Svc
Svc -- Repo
Repo -- DB

' === Mensagens numeradas ===
Cliente -> UI : 1: clicarSalvarFicha(fichaID)
UI -> Ctrl : 1.1: requisitarSalvarFicha()
Ctrl -> Svc : 1.2: validarAutenticacao(cliente)
Svc -> Repo : 1.3: vincularFichaAoCliente(fichaID, clienteID)
Repo -> DB : 1.3.1: UPDATE ficha_treino SET clienteID
DB --> Repo : 1.3.2: confirmação OK
Repo --> Svc : 1.4: fichaSalva = true
Svc --> Ctrl : 1.5: respostaSucesso()
Ctrl --> UI : 1.6: mensagem("Ficha salva com sucesso")
UI --> Cliente : 1.7: exibirConfirmação()
@enduml
```


## Diagrama de Sequencia Registrar Evolução

```plantuml
@startuml
title UC-07 - Registrar Evolução (Layout Vertical)
skinparam backgroundColor #F8FBF9
skinparam componentStyle rectangle
skinparam object {
  BorderColor #555
  BackgroundColor<<Ator>> #F8BBD0
  BackgroundColor<<Interface>> #E1BEE7
  BackgroundColor<<Service>> #C5E1A5
  BackgroundColor<<Repository>> #FFF59D
  BackgroundColor<<Database>> #D7CCC8
}

' === Objetos ===
object "Cliente\n<<Ator>>" as Cliente
object "App Mobile\n<<Interface>>" as UI
object "Controller\n<<Interface>>" as Ctrl
object "ServiceEvolucao\n<<Service>>" as Svc
object "RepoEvolucao\n<<Repository>>" as Repo
object "Banco de Dados\n<<Database>>" as DB

' === Layout vertical ===
Cliente -down-> UI
UI -down-> Ctrl
Ctrl -down-> Svc
Svc -down-> Repo
Repo -down-> DB

' === Mensagens numeradas ===
Cliente -> UI : 1: registrarEvolucao(fichaID, data, progresso)
UI -> Ctrl : 1.1: enviarDadosEvolucao()
Ctrl -> Svc : 1.2: validarFicha(fichaID)
Svc -> Repo : 1.3: salvarEvolucao(clienteID, fichaID, progresso)
Repo -> DB : 1.3.1: INSERT evolucao
DB --> Repo : 1.3.2: confirmação OK
Repo --> Svc : 1.4: retorno sucesso
Svc --> Ctrl : 1.5: mensagem("Evolução registrada")
Ctrl --> UI : 1.6: resposta OK
UI --> Cliente : 1.7: exibirMensagem("Registro concluído")
@enduml
```


---


## Diagrama de Estados

```plantuml
@startuml
title Diagrama de Estados - Sistema FitControl 🏋️‍♂️
skinparam backgroundColor #FAFAFA
skinparam state {
  BackgroundColor<<Auth>> #BBDEFB
  BackgroundColor<<Ficha>> #C8E6C9
  BackgroundColor<<Evolucao>> #FFF59D
  BackgroundColor<<Erro>> #FFCDD2
  BorderColor #444
  FontColor #222
  FontSize 12
  RoundCorner 15
}
skinparam shadowing false
skinparam arrowColor #555

[*] --> Estado_Inicializacao : startApp / carregarConfig()

state Estado_Inicializacao <<Auth>> {
  [*] --> AguardandoLogin
  AguardandoLogin : entry / exibirTelaLogin()
  AguardandoLogin : do / aguardarCredenciais()
  AguardandoLogin --> Autenticando : credenciaisEnviadas / validarUsuario()
  Autenticando : do / verificarCredenciais()
  Autenticando --> SessaoAtiva : [credenciaisVálidas] / iniciarSessao()
  Autenticando --> AguardandoLogin : [credenciaisInválidas] / mostrarErro()
}

Estado_Inicializacao --> SessaoAtiva : autenticaçãoConcluída

state SessaoAtiva {
  [*] --> Dashboard
  Dashboard : entry / exibirMenuPrincipal()
  
  Dashboard --> CriandoFicha : selecionarOpcao("Nova Ficha")
  Dashboard --> VisualizandoFicha : selecionarOpcao("Fichas Salvas")
  Dashboard --> EvoluindoFicha : selecionarOpcao("Registrar Evolução")
  Dashboard --> [*] : logout / encerrarSessao()
}

state CriandoFicha <<Ficha>> {
  [*] --> InserindoExercicios
  InserindoExercicios : do / adicionarExercicios()
  InserindoExercicios --> DefinindoSeries : prosseguir / validarCampos()
  DefinindoSeries : do / inserirSeriesCargas()
  DefinindoSeries --> SalvandoFicha : salvar / validarDados()
  SalvandoFicha : entry / persistirFichaBD()
  SalvandoFicha --> FichaSalva : sucesso / confirmarCriacao()
  FichaSalva --> [*] : retornar / voltarDashboard()
}

state VisualizandoFicha <<Ficha>> {
  [*] --> ListandoFichas
  ListandoFichas : entry / carregarFichasCliente()
  ListandoFichas --> DetalhandoFicha : selecionarFicha(fichaID)
  DetalhandoFicha : do / exibirDetalhesFicha()
  DetalhandoFicha --> [*] : voltar / retornarMenu()
}

state EvoluindoFicha <<Evolucao>> {
  [*] --> ColetandoDados
  ColetandoDados : entry / solicitarProgresso()
  ColetandoDados --> ValidandoDados : prosseguir / verificarCampos()
  ValidandoDados --> SalvandoEvolucao : [dadosValidos] / registrarEvolucaoBD()
  ValidandoDados --> ColetandoDados : [dadosInvalidos] / mostrarErro()
  SalvandoEvolucao --> EvolucaoSalva : sucesso / exibirConfirmacao()
  EvolucaoSalva --> [*] : voltar / retornarDashboard()
}

state ErroSistema <<Erro>> {
  [*] --> NotificarErro
  NotificarErro : entry / exibirMensagemErro()
  NotificarErro --> Dashboard : tentarNovamente()
}

SessaoAtiva --> ErroSistema : erroCritico / registrarLog()
ErroSistema --> SessaoAtiva : recuperar / restaurarSessao()

SessaoAtiva --> [*] : encerrarAplicacao / salvarEstado()
@enduml
```


---

# Modelo de Entidade Relacional 

```plantuml
@startuml
title Modelo Entidade-Relacionamento - FitControl 

' ======= CONFIGURAÇÃO VISUAL =======
skinparam backgroundColor #F9FAFB
skinparam handwritten false
skinparam rectangle {
  StereotypeFontSize 11
  StereotypeFontColor #333
  BorderColor #555
  FontColor #111
  RoundCorner 20
  FontSize 13
}
skinparam entity {
  BorderColor #444
  FontColor black
  FontSize 12
  RoundCorner 20
}

' ======= DEFINIÇÃO DE CORES =======
' Azul: Entidades principais (núcleo administrativo)
' Verde: Entidades secundárias (operacionais)
' Amarelo: Entidades associativas (ligações N:N)
' Roxo: Entidades de resultado (evolução / saída)

rectangle "FitControl - Modelo de Dados" as legenda #FFFFFF {
legend left
|Cor|Categoria|Descrição|
|--|--|--|
|<back:#BBDEFB> </back>|**Principal**|Entidades centrais do sistema|
|<back:#C8E6C9> </back>|**Secundária**|Entidades derivadas e operacionais|
|<back:#FFF59D> </back>|**Associativa**|Tabelas N:N (ligações entre entidades)|
|<back:#E1BEE7> </back>|**Resultado**|Histórico, evolução ou métricas|
endlegend
}

hide circle

' ======= ENTIDADES =======
entity "Academia" as Academia <<Principal>> #BBDEFB {
  *id_academia : UUID
  --
  nome : varchar(120)
  cnpj : varchar(18)
  email : varchar(100)
  telefone : varchar(20)
  endereco : varchar(255)
}

entity "PersonalTrainer" as Personal <<Principal>> #BBDEFB {
  *id_personal : UUID
  --
  id_academia : UUID [FK]
  nome : varchar(120)
  email : varchar(100)
  senha_hash : varchar(255)
  cref : varchar(20)
}

entity "Cliente" as Cliente <<Principal>> #BBDEFB {
  *id_cliente : UUID
  --
  id_academia : UUID [FK]
  nome : varchar(120)
  email : varchar(100)
  senha_hash : varchar(255)
  data_nascimento : date
}

entity "FichaTreino" as Ficha <<Secundaria>> #C8E6C9 {
  *id_ficha : UUID
  --
  id_personal : UUID [FK]
  id_cliente : UUID [FK]
  titulo : varchar(100)
  data_criacao : date
  observacoes : text
}

entity "Exercicio" as Exercicio <<Secundaria>> #C8E6C9 {
  *id_exercicio : UUID
  --
  nome : varchar(100)
  grupo_muscular : varchar(50)
  descricao : text
}

entity "Ficha_Exercicio" as FichaEx <<Associativa>> #FFF59D {
  *id_ficha : UUID [FK]
  *id_exercicio : UUID [FK]
  --
  series : int
  repeticoes : int
  carga : decimal(5,2)
}

entity "Evolucao" as Evolucao <<Resultado>> #E1BEE7 {
  *id_evolucao : UUID
  --
  id_cliente : UUID [FK]
  id_ficha : UUID [FK]
  data_registro : date
  progresso : text
  peso_atual : decimal(5,2)
}

' ======= RELACIONAMENTOS E CARDINALIDADES =======
Academia ||--o{ Personal : "1:N"
Academia ||--o{ Cliente : "1:N"
Personal ||--o{ Ficha : "1:N"
Cliente ||--o{ Ficha : "1:N"
Ficha ||--o{ FichaEx : "1:N"
Exercicio ||--o{ FichaEx : "1:N"
Ficha ||--o{ Evolucao : "1:N"
Cliente ||--o{ Evolucao : "1:N"

' ======= LAYOUT (POSICIONAMENTO VISUAL) =======
left to right direction
Academia -[hidden]-> Personal
Cliente -[hidden]-> Ficha
Ficha -[hidden]-> Evolucao
@enduml
```

