# Matriz e Plano de Segurança da API

**Projeto:** i-Stock Manager  
**Tecnologias:** Spring Boot, Spring Security, Java, JPA/Hibernate e MySQL

---

# 1. Matriz de Segurança da API

| Recurso/Operação             | Ameaça/Vulnerabilidade                    | Possível Impacto                                      | Controle Preventivo                                              |
| ---------------------------- | ----------------------------------------- | ----------------------------------------------------- | ---------------------------------------------------------------- |
| Autenticação                 | Tentativas de acesso indevido             | Acesso não autorizado ao sistema                      | Spring Security, autenticação e senhas protegidas com BCrypt     |
| Login                        | Brute Force                               | Comprometimento da conta do usuário                   | Senhas fortes, controle de tentativas e monitoramento            |
| Senhas                       | Armazenamento em texto puro               | Exposição das credenciais                             | Utilização de BCrypt para armazenar o hash das senhas            |
| Endpoints protegidos         | Acesso sem autenticação                   | Exposição ou alteração indevida de dados              | Autenticação obrigatória nos endpoints protegidos                |
| Autorização                  | Usuário acessar funções administrativas   | Alteração ou exclusão indevida de dados               | Controle de acesso baseado em roles                              |
| Cadastro de produtos         | SQL Injection                             | Manipulação ou acesso indevido ao banco de dados      | JPA/Hibernate, consultas parametrizadas e validação das entradas |
| Alteração de produtos        | Dados maliciosos ou inválidos             | Corrupção ou alteração incorreta dos dados            | Validação dos dados recebidos e controle de autorização          |
| Exclusão de produtos         | Usuário não autorizado realizar exclusões | Perda de dados                                        | Operação permitida somente para ADMIN                            |
| Consulta de produtos         | Acesso não autorizado                     | Exposição de informações                              | Autenticação e autorização dos endpoints                         |
| Dados recebidos pela API     | XSS (Cross-Site Scripting)                | Execução de conteúdo malicioso em aplicações clientes | Validação e sanitização dos dados quando necessário              |
| Comunicação entre aplicações | CORS configurado incorretamente           | Acesso indevido à API por aplicações não autorizadas  | Configuração de origens permitidas                               |
| Requisições autenticadas     | CSRF                                      | Execução de operações em nome de um usuário           | Configuração adequada do mecanismo de autenticação e CSRF        |
| Comunicação com a API        | Interceptação de dados                    | Vazamento de informações                              | Utilização de HTTPS em produção                                  |
| Dados de entrada             | Dados inválidos                           | Erros e inconsistências na aplicação                  | Bean Validation com @Valid, @NotNull, @Size, entre outras        |
| Mensagens de erro            | Exposição de informações internas         | Vazamento de informações da aplicação                 | Tratamento global de exceções e mensagens controladas            |
| Banco de dados               | Exposição de credenciais                  | Comprometimento do banco                              | Uso de variáveis de ambiente para informações sensíveis          |
| Endpoints administrativos    | Escalada de privilégios                   | Usuário comum executar operações administrativas      | Controle de acesso por role utilizando Spring Security           |

---

# 2. Plano de Segurança da API

## 2.1 Autenticação

A API do projeto i-Stock Manager utilizará o Spring Security para realizar a autenticação dos usuários.

O acesso aos endpoints protegidos será permitido somente para usuários autenticados. As credenciais serão verificadas antes que o usuário possa acessar os recursos protegidos.

As senhas não serão armazenadas em texto puro no banco de dados. Será utilizado o algoritmo BCrypt para gerar um hash seguro das senhas.

A autenticação será responsável por identificar o usuário, enquanto a autorização será responsável por determinar quais operações ele poderá realizar.

## 2.2 Recursos públicos

Os recursos relacionados à autenticação poderão ser acessados sem autenticação prévia.

Inicialmente, serão considerados públicos:

- Endpoint de login/autenticação;
- Endpoint de cadastro de usuário, caso disponibilizado;
- Endpoints necessários para recursos públicos da aplicação.

Os demais endpoints deverão exigir autenticação.

Exemplo:

```text
/api/public/** → acesso público
/api/auth/** → autenticação
/api/products/** → usuários autenticados
/api/admin/** → somente administradores
```
