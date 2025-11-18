
* Diagrama de Sequencia Criar Ficha de Treino
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

* Diagrama de Sequencia Salvar Ficha de Treino


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






---


*Diagrama de Estados

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





---

*Modelo de Entidade Relacional 

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
