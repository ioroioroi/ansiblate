# PlantUML
----
**PlantUML** は DSL(ドメイン特化言語) で UML の図を描きます. 拡張子は`.pu`です.

# Diagram
----
UML は大きく 振る舞い図 と 構造図 に分類されます

## Behavior（振る舞い図）
振る舞い図は、振る舞いに関する機能を表現します

| Diagram |	PlantUML | 説明 |
|:--:|:--:|:--:|
| ユースケース図 |	Use Case | 要求される機能を表現 |
| アクティビティ図 | Activity | プロセスの状態を表現 |
| ステートチャート図 | State | オブジェクトの状態を表現 |
| シーケンス図 | Sequence | やり取りするメッセージと順序を表現 |

## Structure（構造図）
構造図は、システムの静的な構造や実装の物理的な要素を表現します

| Diagram | PlantUML | 説明 |
|:--:|:--:|:--:|
| クラス図 | Class | 論理的なビューを表現 |
| オブジェクト図 |	Object | 論理的なビューを表現 |
| コンポーネント図 |	Component | コンポーネントの依存関係を表現 |
| パッケージ図 | Component | パッケージの依存関係を表現 |
| 配置図 |	Component | システムの物理的な配置を表現 |


# UML
----

## Use Case
```
@startuml

actor Promoter
actor Entrant

Promoter --> (Create Event)
Promoter -> (Attend Event)

Entrant --> (Find Event)
(Attend Event) <- Entrant

(Attend Event) <.. (Create Member)  : <<include>>

@enduml
```

## Activity
```
@startuml

(*) --> "Find Event"
"Find Event" -> "Attend Event"

if "Capacity?" then
  ->[ok] "Create Ticket"
else
  -->[full] if "Standby?" then
    ->[ok] "Standby Ticket"
  else
    -->[no] "Cancel Ticket"
    "Cancel Ticket" --> (*)
  endif
endif

"Create Ticket" --> ==show==
"Standby Ticket" --> ==show==
==show== --> "Show Ticket"
"Show Ticket" --> (*)

@enduml
```

## State
```
@startuml

[*] --> active

active -right-> inactive : disable
inactive -left-> active  : enable

inactive --> closed  : close
active --> closed  : close

closed --> [*]

@enduml
```

## Sequence
```
@startuml

actor Entrant

Entrant -> Ticket : Attend Event Request

activate Ticket
Ticket -> Member : Create Member Request

activate Member
Member -> Member : Create Member
Ticket <-- Member : Create Member Response
deactivate Member

Ticket -> Ticket : Create Ticket
Entrant <-- Ticket : Attend Event Response
deactivate Ticket

@enduml
```

## Class
```
@startuml

class User {
  username
  password
  +sign_in()
}

class Group {
  name
}

class Member {
  roles
}

User .. Member
Group .. Member

@enduml
```

## Object
```
@startuml

object User
object Group
object Member

object Event
object Ticket

User . Group
User o.. Member
Group o.. Member

Group o. Event
Event o.. Ticket
Member . Ticket

@enduml
```

## Component
```
@startuml

cloud "Cloud" {
  package "Package" {
    [register]
    frame "frame" {
      [backup]
    }
  }
}

node "Node" {
  database "Database" {
    [store]
  }
  folder "Folder" {
    [File]
  }
}

[register] .. [store] : HTTP
[backup] .. [File] : FTP

@enduml
```

# Common
----
* 参考 : http://qiita.com/ogomr/items/0b5c4de7f38fd1482a48


# ガチ例
-----
```
@startuml

actor User

== Initial ==

User -> Client : Request Client Sign In
note right : GET /user/sign_in

activate Client
Client -> Client : Access Token?

== Authentication ==

Client -> AuthN : Redirect
note right : GET /oauth/authorize
deactivate Client

activate AuthN
AuthN -> AuthN : Current User?
AuthN -> AuthN : Redirect
note right : GET /user/sign_in
User <- AuthN : Response AuthN Sign In
deactivate AuthN

User -> AuthN : Request AuthN Sign In (ID, Pass)

activate AuthN
note right : POST /user/sign_in
AuthN -> AuthN : Redirect
note right : GET /oauth/authorize

== Authorization ==

AuthN -> AuthZ : Redirect
note right : GET /oauth/authorize
deactivate AuthN

activate AuthZ
User <- AuthZ : Response AuthZ Application
deactivate AuthZ

User -> AuthZ : Request AuthZ Application (Allow)
note right : POST /oauth/authorize

activate AuthZ
AuthZ -> AuthZ : Generate Code

Client <- AuthZ : Redirect
note right : GET /callback
deactivate AuthZ

activate Client
Client -> AuthZ : Request Access Token
note right : POST /oauth/access_token

activate AuthZ
AuthZ -> AuthZ : Authorization Code?
AuthZ -> AuthZ : Generate Token

Client <-- AuthZ : Response Access Token
deactivate AuthZ

Client -> Client : Redirect
note right : GET /user/sign_in

Client -> Client : Access Token?

== Resource ==

Client -> Resource : Request User (Access Token)
note right : GET /api/user

activate Resource
Client <-- Resource : Response User
deactivate Resource

== Final ==

Client -> Client : Redirect
note right : GET /

User <- Client : Response Client Sign In
deactivate Client

@enduml
```
