
> **[View README English version](#sentimental-social-media-app-english)**

# Sentimental Social Media App (Português-BR)

Simples **Aplicativo de Mídias Sociais**, baseado em texto, que usa análise de sentimentos para identificar a emoção de uma postagem e mudar sua cor. 

 ### **[SentimentalApp Website](http://sentimental-app-ehfoseuifheh.s3-website-sa-east-1.amazonaws.com/)**

Este é um projeto **Full Stack**, **Aplicação Web** e  com **API RESTful**,  para fins de demonstração apenas.
Possui **Autenticação**, **Modelagem de Dados**, **operações CRUD**, **Segurança**, **Desenvolvimento de API**, **Teste de API**, **Implementação na Nuvem** e **Experiência de Usuário**.

---

### Summary
- [Principais Ferramentas](#principais-ferramentas)
- [Desenvolvimento Back-End](#desenvolvimento-back-end)
	- [Interface de Programação (API)](#interface-de-programação-api)
	- [Autenticação de Usuário](#autenticação-de-usuário)
	- [Medidas de Segurança](#medidas-de-segurança)
	- [Gestão de Erros](#gestão-de-erros)
	- [Modelagem de Dados](#modelagem-de-dados)
	- [Análise de Sentimentos](#análise-de-sentimentos)
- [Desenvolvimento Front-End](#desenvolvimento-front-end)
	- [Funcionalidades](#funcionalidades)
	- [Páginas](#páginas)
	- [Fluxograma](#fluxograma-website)
	- [Capturas de Tela](#capturas)
- [Testando](#testando)
	- [Testes API](#testes-api)
- [Implementação](#implementação)
	- [Hospedagem AWS](#hospedagem-aws)
	- [Hospedagem do Banco de Dados](#hospedagem-do-banco-de-dados)
	- [Microserviços](#arquitetura-de-microserviços-não-implementado)


# Principais Ferramentas

-   Construído utilizando **Node.js**, **Express**, **React**, **MongoDB**, **AWS**

-   Usei **Node.js** com **Express** para implementar as **rotas de API** e a **autenticação**

-   O front-end é feito em **React**, com interface clara e simples

-   Para modelagem de dados **MongoDB**, gerenciado pelo framework **Mongoose**

- 	Arquivos são implementados em **AWS**  

-	Dados de usuário são armazenados em **MongoDB Atlas**

*Usei uma abordagem pouco acoplada com MVC design pattern.*

# Desenvolvimento Back-End

O back-end foi feito em Node e Express, assim como algumas outras bibliotecas para cuidarem da autenticação, proteção e sanitização de dados, comunicação com banco de dados, etc.

**Estrutura de Arquivos** (Back-end)
![backend_flowchart](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/backend_flowchart.png)

- **Main**:
	- **server**: inicia o servidor e conecta com o banco de dados
	- **app**: middlewares para várias funções
	- **router**: roteia endpoints para funções no controller 
- **Controller**: consulta e gerencia o banco de dados
- **Model**: schema para coleções do banco de dados
- **Util**: utilidades para o aplicativo

## Interface de Programação (API)

Eu usei uma API REST com arquitetura Cliente-Servidor, que pode então ser acessado por diferentes meios além do navegador. O token de sessão pode ser armazenado em um cookie ou mandar de volta para o servidor via header **Authorization** na solicitação.

[Acessar Documentação da API](https://documenter.getpostman.com/view/24491479/2s9YC1Xv31)

**Rotas e Métodos HTTP**

| Método | Endpoints e Params | Body | Header | Ação |
|:--:|-- | :--: |:--:|--|
| <font color="PaleGreen">GET | / |  |  | Testar Servidor ou Autenticação
| <font color="PaleGoldenrod">POST | /signup  | `username`, `name`, `password`, <br> `passwordConfirm` |  | Registrar novo Usuário <br>(receber Token Auth)
| <font color="PaleGoldenrod">POST | /login  | `username`, `password` |  | Logar Usuário (receber Token Auth)
| <font color="PaleGreen">GET | /logout  |  |Auth| Sair (invalidar Token)
| <font color="PaleGreen">GET |/posts<font color="Coral">?page=1&limit=10|  |  | Obter últimas Postagens (default = 10) 
| <font color="PaleGreen">GET | /posts/<font color="Coral">:postid |  |  | Obter Postagem única
| <font color="PaleGoldenrod">POST | /posts | `content` | Auth | Criar nova Postagem 
| <font color="Salmon">DELETE| /posts/<font color="Coral">:postid |  | Auth | Apagar Postagem
| <font color="Plum">PATCH | /posts/<font color="Coral">:postid</font>/like |  | Auth | Curtir uma Postagem
| <font color="Plum">PATCH | /posts/<font color="Coral">:postid</font>/unlike |  | Auth | Remover Curtida
| <font color="PaleGoldenrod">POST | /comment/<font color="Coral">:postid</font> | `content` | Auth | Comentar em uma Postagem
| <font color="Salmon">DELETE| /comment/<font color="Coral">:commentid</font> | | Auth | Apagar Comentário
| <font color="PaleGreen">GET | /following/posts|| Auth | Postagens de quem você segue
| <font color="PaleGreen">GET | /liked || Auth | Postagens que você curtiu
| <font color="PaleGreen">GET | /commented || Auth | Postagens que você comentou
| <font color="PaleGreen">GET | /users<font color="Coral">?search=user |||Buscar Usuário por `name` ou `username`
| <font color="PaleGreen">GET | /users || | Obter à si mesmo|
| <font color="PaleGreen">GET | /users/<font color="Coral">:userid || | Obter Usuário|
| <font color="PaleGreen">GET | /users/<font color="Coral">:userid</font>/posts |  |  | Obter Postagens de um Usuário
| <font color="Plum">PATCH | /users/<font color="Coral">:userid</font>/follow |  | Auth | Seguir um Usuário|
| <font color="Plum">PATCH | /users/<font color="Coral">:userid</font>/unfollow |  | Auth | Deixar de Seguir|
| <font color="PaleGreen">GET | /following || Auth | Usuários que você Segue
| <font color="PaleGreen">GET | /followers || Auth | Usuários que Seguem você 
| <font color="Plum">PATCH | /update |`username`, `name`, <br> `about`, `photo(file)` | Auth | Atualizar dados não sensíveis <br> (fields are optional)
| <font color="Plum">PATCH | /password | `currentPassword`, <br> `newPassword`, <br> `newPasswordConfirm`| Auth | Atualizar senha
| <font color="Salmon">DELETE| /delete | `password` | Auth | Apagar Usuário e dados relacionados


*Postman foi usado para testar e gerar a documentação da API.*


## Autenticação de Usuário

Autenticação é controlada no arquivo **authController**, usuários podem se cadastrar ou entrar para receber um token de autenticação.

Usuários autenticados são atribuídos com um [JSON Web Token](https://jwt.io/) (JWT) para troca de chave privada, permitindo a autenticação requerida para rotas e solicitações seguras.
Tokens são válidos por três meses ou até que o usuário mude as credenciais.

Quando a senha é criada ou alterada, antes de ser salva no banco de dados, ela passa pela função de hashing [bcrypt](https://github.com/kelektiv/node.bcrypt.js) para armazenamento seguro. Esta mesma biblioteca é usada depois na entrada para comparar a senha recebida com a hash armazenada.

## Medidas de Segurança

Segurança contra ataques comuns é administrado no arquivo **globalSecurity**.

- Ataques de Injeção:  Sanitização aplicada nos dados da solicitação ([express-mongo-sanitize](https://github.com/fiznool/express-mongo-sanitize))
- Ataques DoS: Solicitações por hora limitadas ([express-rate-limit](https://github.com/express-rate-limit/express-rate-limit))
- Outros Ataques: Gerenciado pelo pacote [helmet](https://github.com/helmetjs/helmet)

## Gestão de Erros

Alguns arquivos, localizados na pasta "utils", são usados para criar e capturar erros:

- **globalError**:  Gerencia erros comuns e envia uma resposta personalizada com 4xx status code
- **appError**: Classe para criação de erros customizados (capturado pelo **globalError** middleware)
- **asyncError**:  Encobre funções *async*  em blocos *try-catch*, para casos de promises rejeitadas


## Modelagem de Dados

**MongoDB** é um banco de dados de documentos que possui flexibilidade, portanto o aplicativo pode atualizar o *schema* sem a necessidade de alterar documentos existentes, e também é completamente integrado com o ambiente Node, utilizando o pacote [mongoose](https://mongoosejs.com/) para performar operações CRUD.

![database_schema](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/database_schema.png)

A coleção **User** armazena dados e credenciais relacionadas ao usuário:

	Table User {
	  _id 				integer [primary key]
	  username 			varchar(20) [unique]
	  password 			varchar(20) 
	  name 				varchar(20) 
	  about 			text
	  photo 			image/base64
	  created_at 			timestamp
	  updated_at 			timestamp
	}
Imagens de perfil são carregadas com [multer](https://github.com/expressjs/multer), redimensionadas com [sharp](https://github.com/lovell/sharp), e então armazenadas no formato base64.

A coleção **Post** armazena conteúdo de texto, e os ids de curtidas e comentários associados á postagem.

	Table Post {
	  id 				integer [primary key]
	  user_id 			integer
	  content 			text
	  sentiment 			value ['Positive', 'Negative', 'Neutral']
	  like_ids 			list [integer]
	  comment_ids 			list [integer]
	  created_at 			timestamp
	}

A coleção **Comment** também armazena conteúdo de texto, com referência de sua postagem e usuário:

	Table Comment {
	  _id				integer [primary key]
	  user_id			integer
	  post_id			integer
	  content			text
	  created_at			timestamp 
	}

Ambas **Follow** e **Like** são usadas como tabelas de referência. A coleção **Follow** associa o usuário atual à quem ele está seguindo, e a coleção **Like** associa uma postagem ao usuário atual:


	Table Like {
	  user_id 			integer
	  post_id 			integer 
	  created_at 			timestamp 
	}

	Table Follow {
	  follower_id 			integer 
	  followed_id 			integer
	  created_at 			timestamp 
	}


*Arquivos de schema são armazenados na pasta models.*

O banco de dados está hospedado em [MongoDB Atlas](https://www.mongodb.com/cloud/atlas), um serviço gerenciado e baseado na nuvem (DB as a Service).

## Análise de Sentimentos

Eu usei o AFINN dataset com o pacote [sentiment](https://github.com/thisandagain/sentiment), pela sua simples lógica e, portanto, desempenho.

**Como funciona:**
Para cada postagem que um usuário cria, o algoritmo verifica cada palavra contra uma lista de palavras, que são pontuadas de acordo com o quão positivas ou negativas elas são, então realiza um comparativo simples considerando todas as palavras da postagem.
Um "sentimento geral" é retornado, com um dos valores possíveis ['Positive', 'Negative', 'Neutral'] para ser incorporado no documento da postagem.

> AFINN is a list of words rated for valence with an integer between
> minus five (negative) and plus five (positive). Sentiment analysis is
> performed by cross-checking the string tokens (words, emojis) with the
> AFINN list and getting their respective scores. The comparative score
> is simply: `sum of each token / number of tokens`.

*Este algoritmo pode ser substituído por um mais avançado, para incluir recursos aprimorados, mas possivelmente aumento no tempo de processamento.*

# Desenvolvimento Front-End

O Web front-end foi feito em [React](https://react.dev/) e se comunica com a API usando [React Query](https://github.com/TanStack/query), as solicitações e respostas podem ser vistas no console:

    REQUEST		posts?page=1 {method: 'GET', headers: {…}}
    RESPONSE	{status: 'success', page: 1, totalPages: 15, results: 10, …}
    REQUEST  	posts {method: 'POST', headers: {…}, body: '{"content":"Hey there!"}'}
	RESPONSE  	{status: 'success', userid: '64f56a57a285fc672c6d45', data: {…}}
	...

**Estrutura de Arquivos** (Front-end)
![frontend_flowchart](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/frontend_flowchart.png)

- **App**: componente principal, parente dos componentes de páginas
- **Contexts**: React Contexts para gerenciamento de estado global
- **Hooks**: React Hooks personalizados, para algumas funcionalidades
- **Pages**: componentes de páginas, para cada funcionalidade
- **UI**: componentes únicos, estáticos ou interativos, para a composição de páginas
- **Services**: utilities being used by the app

## Funcionalidades

**Contexts** (Estado Global):
- **AuthContext**: Define dados do usuário e token de autenticação acessíveis em todo o aplicativo
- **DarkmodeContext**: Define o modo de estilo atual (CSS variables)
- **LanguageContext**:  Define o idioma atual, busca a tradução para elementos de texto na interface
- **ModalContext**:  Exibe uma janela modal em qualquer página para obter confirmação de ações relevantes
- **StatsMessageContext**:  Exibe mensagens para notificar o usuário sobre alertas importantes ou erros

**Hooks** (Lógica Customizada):
- **useLocalStorage**: Lógica para salvar dados no armazenamento local
- **useAccess**: Lógica para autenticar o usuário e salva seu estado localmente
- **useUpdate**: Lógica para atualizar dados do usuário ou apagar conta
- **useFollow**: Lógica para seguir ou deixar de seguir um usuário

**Análise de Sentimentos**:
- Já que o [algoritmo](#sentiment-analysis) nos dá um valor por palavra, é possível utilizá-lo para classificar cada palavra enquanto uma nova postagem é escrita.
- O valor geral do texto pode ser <font color="PaleTurquoise">**Neutro**</font>, <font color="LightGreen">**Positivo**</font>, ou <font color="IndianRed">**Negativo**</font>, e a cor associada é atribuída ao plano de fundo da postagem.

![new_post](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/new_post.png)

## Páginas

As rotas de navegação foram implementadas com [React Router](https://reactrouter.com/).

| Componente | URL | Restrito | Descrição |
|:--: |--|:--: |--|
| Login | <font color="Gray">sentimental.app</font>/login |Não| Autenticar Usuário
| Signup | <font color="Gray">sentimental.app</font>/signup |Não| Registrar novo Usuário
| Timeline | <font color="Gray">sentimental.app</font>/ |Não| Linha do Tempo pública (Página Principal)
| Search | <font color="Gray">sentimental.app</font>/search |Não| Buscar por um Usuário
| Profile | <font color="Gray">sentimental.app</font>/profile<font color="Gray">/:userid</font> |Não| Perfil de Usuário
| EditProfile | <font color="Gray">sentimental.app</font>/editprofile |Sim| Editar seu Perfil
| Posts | <font color="Gray">sentimental.app</font>/posts |Sim| Postagens de quem você segue
| Liked | <font color="Gray">sentimental.app</font>/liked |Sim| Postagens que você curtiu
| Commented | <font color="Gray">sentimental.app</font>/commented |Sim| Postagens que você comentou
| Following | <font color="Gray">sentimental.app</font>/following |Sim| Usuários que você segue
| Followers| <font color="Gray">sentimental.app</font>/followers |Sim| Usuários que seguem você
| Unauthorized | <font color="Gray">sentimental.app</font>/unauthorized |Não| Rota não autorizada
| Error | <font color="Gray">sentimental.app</font>/error |Não| Rota não encontrada

*Páginas restritas podem ser acessadas apenas quando o usuário está autenticado.*

## Fluxograma Website

Este fluxograma mostra como navegar pelo website, autenticação, e rotas restritas.

![flowchart_website](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/flowchart_website.png)

## Capturas

**Acesso**
![login](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/login.png)

**Linha do Tempo** 
![timeline](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/timeline.png)

**Perfil de Usuário**
![profile](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/profile.png)

**Buscar Usuário** 
![search](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/search.png)

**Design Responsivo (Celular)📱**
![iphone_mockup](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/iphone_mockup.png)

**Design Responsivo (Tablet)**
![ipad_mockup](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/ipad_mockup.png)



# Testando

- Os testes de interface foram executados manualmente durante o desenvolvimento, com Chrome e React Dev Tools.
- Testar o back-end foi bem direto, eu usei a ferramenta **Postman** para escrever alguns testes funcionais automatizados para as [rotas API](#interface-de-programação-api).

## Testes API

[Acessar Documentação da API](https://documenter.getpostman.com/view/24491479/2s9YC1Xv31)

Os seguintes testes foram performados

1.  **Registro de Usuário**:  Checa se o registro foi válido, espera token na resposta
2.  **Acesso de Usuário**: Verifica se o foi logado com sucesso, espera token na resposta
3.  **Obter Usuário**:  Obtém um usuário específico por id
4.  **Criar uma Postagem**: Cria uma nova postagem e salva o id
5.  **Obter Postagem**: Obtém uma postagem específica por id
6.  **Comentar Postagem**: Verifica se um comentário pode ser adicionado a postagem
7.  **Apagar Comentário**: Verifica se um comentário pode ser excluído com sucesso
8.  **Curtir Postagem**: Curte a postagem, esperando uma mensagem de sucesso
9.  **Remover Curtida**: Remove a curtida da postagem, esperando uma mensagem de sucesso
10.  **Apagar Postagem**: Verifica se uma postagem pôde ser excluída com sucesso
11.  **Atualizar Usuário**: Verifica se o perfil de um usuário pôde ser atualizado com sucesso
12.  **Apagar Usuário**: Apaga o usuário e dados criados

**Registro de Usuário** (POST /signup) <font color="yellowgreen">&#10003;</font>

    pm.test("User Registration", function () {
        pm.response.to.have.status(201);
        pm.response.to.have.jsonBody("message", "User registered successfully");
        pm.response.to.have.jsonBody("token");
    });

**Acesso de Usuário** (POST /login) <font color="yellowgreen">&#10003;</font>

    pm.test("User Login", function () {
        pm.response.to.have.status(201);
        pm.response.to.have.jsonBody("token");
    });

**Obter Usuário** (GET /users) <font color="yellowgreen">&#10003;</font>

    pm.test("Get User Profile", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("data");
    });

**Criar uma Postagem** (POST /posts) <font color="yellowgreen">&#10003;</font>

    pm.test("Create a Post", function () {
        pm.response.to.have.status(201);
        pm.response.to.have.jsonBody("message", "Post created successfully");
    });
    
**Obter Postagem** (GET /posts/:postid) <font color="yellowgreen">&#10003;</font>

    pm.test("Get Post", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("post");
    });

**Comentar Postagem** (POST /comment/:postid) <font color="yellowgreen">&#10003;</font>

    pm.test("Comment on Post", function () {
        pm.response.to.have.status(201);
    });

**Apagar Comentário** (DELETE /comment/:commentid) <font color="yellowgreen">&#10003;</font>

    pm.test("Delete Comment", function () {
        pm.response.to.have.status(200);
    });

**Curtir Postagem** (PATCH /posts/:postid/like) <font color="yellowgreen">&#10003;</font>

    pm.test("Like Post", function () {
	    pm.response.to.have.status(200)
	    pm.response.to.have.jsonBody("status", "success")
    });

**Remover Curtida** (PATCH /posts/:postid/unlike) <font color="yellowgreen">&#10003;</font>

    pm.test("Remove Like", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("status", "success")
    });

**Apagar Postagem** (DELETE /posts/:postid) <font color="yellowgreen">&#10003;</font>

    pm.test("Delete Post", function () {
        pm.response.to.have.status(200);
    });

**Atualizar Usuário** (PATCH /update) <font color="yellowgreen">&#10003;</font>

    pm.test("Update User Profile", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("message", "User profile updated");
    });

**Apagar Usuário** (DELETE /delete) <font color="yellowgreen">&#10003;</font>

    pm.test("Delete User", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("message", "User deleted");
    });

**Resultados da Execução**:

![test_results](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/test_results.png)

# Implementação

**Diagrama**
![deployment_diagram](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/deployment.png)

Pelo propósito deste projeto eu estou usando uma implementação simples com arquitetura monolítica, apenas separando os ambientes de back-end e front-end.

## Hospedagem AWS

- Estou usando AWS para a hospedagem do website, uma configuração simples onde os arquivos estáticos do front-end estão armazenados em um S3 bucket, o qual contém uma funcionalidade para hospedagem de Single-Page Web Applications.
- O back-end está implementado em ECS, um serviço para a gestão de containers, e usei Docker para criar o container da aplicação para e rodar no servidor.

## Hospedagem do Banco de Dados
- [MongoDB Atlas](https://www.mongodb.com/atlas/database), um banco de dados on-line gerenciado que oferece fácil implementação e escalabilidade, eu configurei a comunicação com o pacote [mongoose](https://mongoosejs.com/) para Node.js.

![database_screenshot](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/mongodb_screenshot.png)

## Arquitetura de Microserviços (Não Implementado)

Um cenário mais ideal para escalar esse projeto, seria alocando mais recursos, separando cada serviço em seu próprio ambiente (**Containers**), e implementando um **LoadBalancer** e um **EventBus**, que permite comunicação assíncrona entre os serviços.

A imagem mostra uma solução utilizado **Arquitetura de Microserviços** e **AWS Serverless**:

![backend_microservices](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/microservices.png)

---

> **[Voltar ao início](#sentimental-social-media-app-português-br)**



# Sentimental Social Media App (English)

Simple text-based **Social Media App**, that uses emotion-based sentiment analysis to identify the mood of a post and change its color.

 ### **[SentimentalApp Website](http://sentimental-app-ehfoseuifheh.s3-website-sa-east-1.amazonaws.com/)**

[//]: <> (<iframe src="http://localhost:5173/" width="950px" height="700px"></iframe>)

This is a **Full Stack** **Web Application** and **RESTful API** project, for demonstration purposes only.
It features **Authentication**, **Data Modeling**, **CRUD operations**, **Security**, **API Development**, **API Testing**, **Cloud Deployment**, and **User Experience**.

---

### Summary
- [Main Tools and Frameworks](#main-tools-and-frameworks)
- [Back-End Development](#back-end-developent)
	- [API](#api)
	- [User Authentication](#user-authentication)
	- [Security Measures](#security-measures)
	- [Error Handling](#error-handling)
	- [Data Modeling](#data-modeling)
	- [Sentiment Analysis](#sentiment-analysis)
- [Front-End Development](#front-end-development)
	- [Features](#features)
	- [Pages](#pages)
	- [Flowchart](#flowchart-website)
	- [Screenshots](#screenshots)
- [Testing](#testing)
	- [API Tests](#api-tests)
- [Deployment](#deployment)
	- [AWS Hosting](#aws-hosting)
	- [Database Hosting](#database-hosting)
	- [Microservices](#microservices-architecture-not-implemented)


# Main Tools and Frameworks

-   Built using **Node.js**, **Express**, **React**, **MongoDB**, **AWS**

-   It uses **Node.js** with **Express** framework to implement the ** API routes** and **authentication**

-   The front-end is built with **React**, with a simple and clean interface

-   For modeling data the **MongoDB** database, managed by the **Mongoose** framework

- 	Files are deployed on **AWS**  

-	User data is stored on **MongoDB Atlas**

*I used a loosely coupled approach with the MVC design pattern.*

# Back-End Development

The back-end was built with Node and Express, as well as some other libraries to handle authentication, data protection and sanitization, database communication, etc.

**File Structure** (Back-end)
![backend_flowchart](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/backend_flowchart.png)

- **Main**:
	- **server**: starts server and connects to database
	- **app**: middlewares for various features
	- **router**: routes endpoints to functions in the controller 
- **Controller**: query and manage DB
- **Model**: schema for DB collections
- **Util**: utilities for the app

## API

I used a REST API with Client-Server architecture, so that it can be accessed by different means besides the browser. The session token can be stored in a cookie and send back to the server via the **Authorization** header in the request.

[Access API Documentation](https://documenter.getpostman.com/view/24491479/2s9YC1Xv31)

**Routes and HTTP Methods**

| Method | Endpoints and Params | Body | Header| Action |
|:--:|-- | :--: |:--:|--|
| <font color="PaleGreen">GET | / |  |  | Test server and Authentication
| <font color="PaleGoldenrod">POST | /signup  | `username`, `name`, `password`, <br> `passwordConfirm` |  | Register new User <br>(receive Auth Token)
| <font color="PaleGoldenrod">POST | /login  | `username`, `password` |  | Login User (receive Auth Token)
| <font color="PaleGreen">GET | /logout  |  |Auth| Logout User (invalidate Token)
| <font color="PaleGreen">GET |/posts<font color="Coral">?page=1&limit=10|  |  | Get latest Posts (default = 10) 
| <font color="PaleGreen">GET | /posts/<font color="Coral">:postid |  |  | Get single Post 
| <font color="PaleGoldenrod">POST | /posts | `content` | Auth | Create a new Post 
| <font color="Salmon">DELETE| /posts/<font color="Coral">:postid |  | Auth | Delete a Post
| <font color="Plum">PATCH | /posts/<font color="Coral">:postid</font>/like |  | Auth | Like a Post
| <font color="Plum">PATCH | /posts/<font color="Coral">:postid</font>/unlike |  | Auth | Unike a Post
| <font color="PaleGoldenrod">POST | /comment/<font color="Coral">:postid</font> | `content` | Auth | Comment in a Post
| <font color="Salmon">DELETE| /comment/<font color="Coral">:commentid</font> | | Auth | Delete Comment
| <font color="PaleGreen">GET | /following/posts|| Auth | Posts from who you follow
| <font color="PaleGreen">GET | /liked || Auth | Posts you liked
| <font color="PaleGreen">GET | /commented || Auth | Posts you have commented on
| <font color="PaleGreen">GET | /users<font color="Coral">?search=user |||Query User by `name` or `username`
| <font color="PaleGreen">GET | /users || | Get itself|
| <font color="PaleGreen">GET | /users/<font color="Coral">:userid || | Get single User|
| <font color="PaleGreen">GET | /users/<font color="Coral">:userid</font>/posts |  |  | Get Posts from a User
| <font color="Plum">PATCH | /users/<font color="Coral">:userid</font>/follow |  | Auth | Follow a User|
| <font color="Plum">PATCH | /users/<font color="Coral">:userid</font>/unfollow |  | Auth | Unfollow a User|
| <font color="PaleGreen">GET | /following || Auth | Users you are following
| <font color="PaleGreen">GET | /followers || Auth | Users who follow you
| <font color="Plum">PATCH | /update |`username`, `name`, <br> `about`, `photo(file)` | Auth | Update non-sensitive data <br> (fields are optional)
| <font color="Plum">PATCH | /password | `currentPassword`, <br> `newPassword`, <br> `newPasswordConfirm`| Auth | Update password
| <font color="Salmon">DELETE| /delete | `password` | Auth | Delete User and its related data


*Postman was used to test and generate the API documentation.*


## User Authentication

Authentication is handled in the **authController** file, users can sign up or log in to receive an authentication token.

Logged users are assigned with a [JSON Web Token](https://jwt.io/) (JWT) for a private key exchange, allowing required authentication for secured routes and requests.
Tokens are valid for three months or until the user changes credentials.

When the password is created or changed, before it is saved in the database, it passes through the [bcrypt](https://github.com/kelektiv/node.bcrypt.js) hashing function for secure storage. The same library is used later in the log in phase, for comparing the password received with the stored hash.

## Security Measures
Security against common attacks is handled in the **globalSecurity** file.
- Injection attacks:  Applied sanitization on the request data ([express-mongo-sanitize](https://github.com/fiznool/express-mongo-sanitize))
- DoS attacks: Limited amount of requests per user per hour ([express-rate-limit](https://github.com/express-rate-limit/express-rate-limit))
- Other attacks: Managed by the [helmet](https://github.com/helmetjs/helmet) package

## Error Handling
Some files, located in the utils folder, are used for creating and catching errors:
- **globalError**:  Handle all common errors, send a custom response with a 4xx status code
- **appError**: Class blueprint for custom error creation (caught by **globalError** middleware)
- **asyncError**:  Wraps *async* functions in *try-catch* blocks, in case of rejected promises


## Data Modeling

**MongoDB** is a document database that has flexibility, so the app can update the schema without changing the existing documents, and it also has full integration with the Node environment, using the [mongoose](https://mongoosejs.com/) package to perform CRUD operations.

![database_schema](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/database_schema.png)

The **User** collection stores user-related data and credentials:

	Table User {
	  _id 				integer [primary key]
	  username 			varchar(20) [unique]
	  password 			varchar(20) 
	  name 				varchar(20) 
	  about 			text
	  photo 			image/base64
	  created_at 			timestamp
	  updated_at 			timestamp
	}
Profile images are uploaded with [multer](https://github.com/expressjs/multer), resized with [sharp](https://github.com/lovell/sharp), and then stored in base64 format.

The **Post** collection stores text content, and the likes and comments ids associated with it:

	Table Post {
	  id 				integer [primary key]
	  user_id 			integer
	  content 			text
	  sentiment 			value ['Positive', 'Negative', 'Neutral']
	  like_ids 			list [integer]
	  comment_ids 			list [integer]
	  created_at 			timestamp
	}

The **Comment** collection also stores text content, with a reference for its post and user:

	Table Comment {
	  _id				integer [primary key]
	  user_id			integer
	  post_id			integer
	  content			text
	  created_at			timestamp 
	}

Both **Follow** and **Like** are used as referencing tables. The **Follow** collection associates the current user with who the user is following, and the **Like** collection associates a post with the current user:


	Table Like {
	  user_id 			integer
	  post_id 			integer 
	  created_at 			timestamp 
	}

	Table Follow {
	  follower_id 			integer 
	  followed_id 			integer
	  created_at 			timestamp 
	}


*Schema files are located in the models folder.*

The database is hosted on [MongoDB Atlas](https://www.mongodb.com/cloud/atlas), a managed cloud-based DB as a Service.

## Sentiment Analysis 

I used the AFINN dataset with the [sentiment](https://github.com/thisandagain/sentiment) package, for its simple logic and, therefore, performance.

**How it works:**
For each Post a user creates, the algorithm checks each word against a list of words, that are scored on how much positive or negative they are, then it performs a simple comparative considering all words in the sentence.
An overall sentiment is returned with the possible values ['Positive', 'Negative', 'Neutral'] to be embedded in the Post document.

> AFINN is a list of words rated for valence with an integer between
> minus five (negative) and plus five (positive). Sentiment analysis is
> performed by cross-checking the string tokens (words, emojis) with the
> AFINN list and getting their respective scores. The comparative score
> is simply: `sum of each token / number of tokens`.



*This algorithm can be replaced by a more advanced one, to include enhanced features, but possibly increased processing time.*


# Front-End Development

The Web front-end was built with [React](https://react.dev/) and communicates with the API using [React Query](https://github.com/TanStack/query), the requests and responses can be viewed from the console:

    REQUEST		posts?page=1 {method: 'GET', headers: {…}}
    RESPONSE	{status: 'success', page: 1, totalPages: 15, results: 10, …}
    REQUEST  	posts {method: 'POST', headers: {…}, body: '{"content":"Hey there!"}'}
	RESPONSE  	{status: 'success', userid: '64f56a57a285fc672c6d45', data: {…}}
	...


**File Structure** (Front-end)
![frontend_flowchart](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/frontend_flowchart.png)

- **App**: main component, parent of the page components
- **Contexts**: React Contexts for global state management
- **Hooks**: custom React Hooks for some functionalities
- **Pages**: page components for each feature
- **UI**: single static or interactive components for page composition
- **Services**: utilities being used by the app

## Features

**Contexts** (Global State):
- **AuthContext**: Sets user data and authentication token accessible throughout the app
- **DarkmodeContext**:  Sets the current style mode (defined by CSS variables)
- **LanguageContext**:  Sets the current language, searches a translation dictionary for UI text elements
- **ModalContext**:  Displays a modal window on any page  to get confirmation of relevant actions
- **StatsMessageContext**:  Displays messages on any page to notify the user  of  important alerts or errors

**Hooks** (Custom Logic):
- **useLocalStorage**: Logic for saving data to local storage
- **useAccess**: Logic to authenticate the user and save its state locally
- **useUpdate**: Logic to update user data, or delete user account
- **useFollow**: Logic to follow or unfollow a user

**Sentiment Analysis**:
- Since the [algorithm](#sentiment-analysis) gives us a value for each word, we can use it to classify each word while writing a new post. 
- The overall value can be <font color="PaleTurquoise">**Neutral**</font>, <font color="LightGreen">**Positive**</font>, or <font color="IndianRed">**Negative**</font>, then the associated color is attributed to the post's background.

![new_post](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/new_post.png)

## Pages

The navigation routes are implemented with [React Router](https://reactrouter.com/).

| Component | URL | Restrict | Description |
|:--: |--|:--: |--|
| Login | <font color="Gray">sentimental.app</font>/login |No| Authenticate User
| Signup | <font color="Gray">sentimental.app</font>/signup |No| Register a new User
| Timeline | <font color="Gray">sentimental.app</font>/ |No| Public Timeline (Main Page)
| Search | <font color="Gray">sentimental.app</font>/search |No| Search for a User
| Profile | <font color="Gray">sentimental.app</font>/profile<font color="Gray">/:userid</font> |No| User Profile
| EditProfile | <font color="Gray">sentimental.app</font>/editprofile |Yes| Edit your Profile
| Posts | <font color="Gray">sentimental.app</font>/posts |Yes| Posts from who you follow
| Liked | <font color="Gray">sentimental.app</font>/liked |Yes| Posts that you liked
| Commented | <font color="Gray">sentimental.app</font>/commented |Yes| Posts you have commented on
| Following | <font color="Gray">sentimental.app</font>/following |Yes| Users you follow
| Followers| <font color="Gray">sentimental.app</font>/followers |Yes| Users that are following you
| Unauthorized | <font color="Gray">sentimental.app</font>/unauthorized |No| Route not authorized
| Error | <font color="Gray">sentimental.app</font>/error |No| Route not found

*Restricted pages can only be accessed when the user is authenticated.*

## Flowchart Website

This flowchart shows how to navigate the website, authentication, and restricted routes.

![flowchart_website](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/flowchart_website.png)




## Screenshots 

**Log In**
![login](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/login.png)

**Timeline** 
![timeline](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/timeline.png)

**User Profile**
![profile](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/profile.png)

**Search User** 
![search](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/search.png)

**Responsive Design (Phone)📱**
![iphone_mockup](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/iphone_mockup.png)

**Responsive Design (Tablet)**
![ipad_mockup](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Screenshots%20Website/ipad_mockup.png)



# Testing

- The UI tests were performed manually during development, with the Chrome and React Dev Tools.
- Testing the back-end was pretty straightforward, I used the **Postman** tool to write some automated functional  tests for the [API routes](#api). 


## API Tests

[Acessar Documentação da API](https://documenter.getpostman.com/view/24491479/2s9YC1Xv31)

The following tests were performed:

1.  **User Registration**: Checks if the registration was valid, expects a token in the response
2.  **User Login**: Verifies if the user logged in successfully, expects a token in the response
3.  **Get User**: Get a specific user by ID
4.  **Create a Post**:  Creates a new post and saves the ID
5.  **Get Post**: Retrieves a specific post by ID
6.  **Comment on Post**: Checks if a comment can be added to a post
7.  **Delete Comment**: Verifies that a comment can be deleted successfully
8.  **Like Post**: Likes the post, expecting a success message
9.  **Remove Like**: Removes the like from the post, expecting a success message
10.  **Delete Post**: Verifies that a post can be deleted successfully
11.  **Update User Profile**: Checks if a user's profile can be updated successfully
12.  **Delete User**: Ensures that a user can be deleted along with their related data

**User Registration** (POST /signup) <font color="yellowgreen">&#10003;</font>

    pm.test("User Registration", function () {
        pm.response.to.have.status(201);
        pm.response.to.have.jsonBody("message", "User registered successfully");
        pm.response.to.have.jsonBody("token");
    });

**User Login** (POST /login) <font color="yellowgreen">&#10003;</font>

    pm.test("User Login", function () {
        pm.response.to.have.status(201);
        pm.response.to.have.jsonBody("token");
    });

**Get User** (GET /users) <font color="yellowgreen">&#10003;</font>

    pm.test("Get User Profile", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("data");
    });

**Create a Post** (POST /posts) <font color="yellowgreen">&#10003;</font>

    pm.test("Create a Post", function () {
        pm.response.to.have.status(201);
        pm.response.to.have.jsonBody("message", "Post created successfully");
    });
    
**Get Post** (GET /posts/:postid) <font color="yellowgreen">&#10003;</font>

    pm.test("Get Post", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("post");
    });

**Comment on a Post** (POST /comment/:postid) <font color="yellowgreen">&#10003;</font>

    pm.test("Comment on Post", function () {
        pm.response.to.have.status(201);
    });

**Delete Comment** (DELETE /comment/:commentid) <font color="yellowgreen">&#10003;</font>

    pm.test("Delete Comment", function () {
        pm.response.to.have.status(200);
    });

**Like a Post** (PATCH /posts/:postid/like) <font color="yellowgreen">&#10003;</font>

    pm.test("Like Post", function () {
	    pm.response.to.have.status(200)
	    pm.response.to.have.jsonBody("status", "success")
    });

**Remove Like from a Post** (PATCH /posts/:postid/unlike) <font color="yellowgreen">&#10003;</font>

    pm.test("Remove Like", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("status", "success")
    });

**Delete a Post** (DELETE /posts/:postid) <font color="yellowgreen">&#10003;</font>

    pm.test("Delete Post", function () {
        pm.response.to.have.status(200);
    });

**Update User** (PATCH /update) <font color="yellowgreen">&#10003;</font>

    pm.test("Update User Profile", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("message", "User profile updated");
    });

**Delete User** (DELETE /delete) <font color="yellowgreen">&#10003;</font>

    pm.test("Delete User", function () {
        pm.response.to.have.status(200);
        pm.response.to.have.jsonBody("message", "User deleted");
    });

**Run Results**:

![test_results](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/test_results.png)

# Deployment

**Diagram**
![deployment_diagram](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/deployment.png)

For the purpose of this project I am using a simple deployment with a monolithic architecture, only separating the back-end from the front-end environments.

## AWS Hosting
- I am using AWS to host the website, a simple configuration where the built static files of the front-end are stored in an S3 bucket, which has a feature for Single-Page Web Applications hosting.
- The back-en is deployed in an ECS, a service for container orchestration, and I used Docker to containerize the application to run it on the server.


## Database Hosting
- [MongoDB Atlas](https://www.mongodb.com/atlas/database), an online managed database as a service, it offers easy deployment and scalability, then I set the communication to it with the [mongoose](https://mongoosejs.com/) package for Node.js.

![database_screenshot](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/mongodb_screenshot.png)

## Microservices Architecture (Not Implemented)

A more ideal, real-world scenario for scaling this project, would be allocating more resources, separating each service into its own environment (**Containers**), and implementing a **LoadBalancer** and an **EventBus**, that allows asynchronous communication among services.

The image shows a solution using **Microservices Architecture** and **Serverless AWS**:

![backend_microservices](https://raw.githubusercontent.com/lukkksm4/sentimental-app/main/resources/Flowcharts/microservices.png)

---

Credits: [lukkks.m4](https://github.com/lukkksm4)
