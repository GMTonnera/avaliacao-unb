# Guia de Desenvolvimento 

## Informações

- Autor: Gustavo Mello Tonnera
- Repositório: 

## Objetivo

Esse documento tem como objetivo guiar os leitores no processo de desenvolvimento de um sistema de avaliação de turmas da UnB, por meio de uma abordagem didática, a fim de que os o público consiga ter uma visão geral, ao final da leitura, do processo de desenvolvimento de um projeto.

## Requisitos

Parte-se do princípio de que os leitores desse guia estejam familiarizados com: 
- JavaScript;
- TypeScript;
- Bancos de Dados;
- SQL.

## Sumário

1. Especificações do projeto
    
    1.1 Descrição do projeto

    1.2 Requisitos do projeto

2. Design do banco de dados
3. Configuração do repositório
4. PrismaORM
5. 
6. Arquitetura do sistema
7. 

## 1. Especificações do projeto

### 1.1 Descrição do projeto

Como descrito na sessão de Objetivo, será desenvolvido um sistema de avaliação de turmas da Unb. Esse sistema tem como objetivo fornecer uma plataforma na qual estudantes da UnB possam compartilhar suas experiências na universidade por meio de avaliações de turmas e de professores. Essa temática foi escolhida para o projeto do guia, uma vez que o público alvo desse guia são os futuros desenvolvedores da CJR, Empresa Júnior de Computação da UnB.

Esse mesmo projeto foi utilizado por um professor da Unb como trabalho final da disciplina de Banco de Dados, porém os requisitos do projeto foram alterados. Além disso, será reaproveitado alguns arquivos CSVs para popular o banco de dados do sistema. 

### 1.2 Requisitos do projeto

- O usuário deve ser capaz de criar uma conta no sistema;
- O usuáiro precisa estar logado para realizar avaliações;
- O usuário não precisa estar logado para visualizar avaliações e as turmas que podem ser avaliadas;
- O usuário pode realizar comentários em uma avaliação;
- O usuário pode visualizar seu perfil e alterar suas informações, quando logado;
- O administrador deve ser capaz de logar no sistema;
- O administrador deve ser capaz de apagar, alterar ou cadastrar um usuário;
- O administrador deve ser capaz de apagar, alterar ou cadastrar uma disciplina;
- O administrador deve ser capaz de apagar, alterar ou cadastrar uma turma;
- O administrador deve ser capaz de apagar, alterar ou cadastrar um departamento;
- O administrador deve ser capaz de apagar, alterar ou cadastrar uma avaliação;
- O administrador deve ser capaz de apagar, alterar ou cadastrar um comentário;
- O administrador deve ser capaz de apagar, alterar ou cadastrar um professor;
- Um departamento possui disciplinas e professores;
- Uma turma possui uma disciplina e é ministrada por um professor.

## 2. Design do banco de dados

Nessa etapa inicial do projeto, discutiremos como modelar os dados do projeto para, então, construir o modelo do banco de dados. Começaremos modelando os usuários do nosso sistema: os estudantes da Unb e os administradores do sistema. 

Um estudante da Unb é um indivíduo que está cadastrado na universidade e frequenta as turmas ofertadas. Então, seria interessante armazenar um dado que ligue o usuário à Unb. Para esse projeto, foi escolhido a matrícula do estudante e assumiremos que toda matrícula usada no cadastro de fato está ativa no sistema da Unb. Além disso, é interessante armazenar o nome do estudante, curso e email. Poderíamos armazenar outras informações relacionadas ao estudante, como sexo, gênero, idade e semestre, porém isso acrescentaria demasiada complexidade, uma vez que, por exemplo, no caso do semestre precisaríamos construir um mecanismo para atualizar o semestre dos usuário. Assim, armazenaremos apenas a matrícula, nome completo, apelido, curso e email dos usuários. Não é necessário armazenar o apelido, porém, muitas vezes, uma pessoa na UnB é conhecida por um apelido.

O administrador do sistema é o usuário resposável por administrar as contas, avaliações e outras informações do sistema. Já que o público alvo desse são estudantes da Unb, modelaremos os administradores como estudantes da Unb com mais funcionalidades. Logo, cada administrador precisa ter uma matrícula, nome completo, curso e um email.

Agora que temos os usuários do sistema definidos, podemos modelar o objeto mais importante do sistema: a avaliação. Uma avaliação deve ser feita por um usuário (estudante ou administrador) e pode avaliar uma turma. Então, cada avaliação possui uma turma e um usuário assossiados. Além disso, é necessário criar um sistema de avaliação para que os usuários possam metrificar sua experiência. Para esse projeto, foi decidido que a métrica será uma nota de 0 a 10, para simplificar o processo, e o usuário poderá inserir um comentário na avaliação descrevendo os motivos da nota. Assim, para cada avaliação, precisamos armazenar o usuário que fez a avaliação, a turma avaliada, a nota e o comentário.

Com as avaliações modeladas, podemos, então, modelar os comentários. Comentários são, simplesmente, mensagens realizadas por usuários em avaliações. Então, um comentário está associado ao usuário que fez o comentário e a uma avaliação e armazena a mensagem do usuário.

Finalmemte, podemos nos concentrar em modelar as turmas. Como dito arteriormente, faremos uso de arquivos CSVs para popular o banco de dados. Cada arquivo CSVs armazena um dos seguintes dados: turmas, disicplina ou departamentos. Para, novamente, adicionar complexidade desnecessária no projeto, aproveitaremos a forma como esses dados foram modelados nos arquivos no projeto como um todo. Assim, um departamento possui um nome e um código, uma disciplina possui um código e um nome e está assossiada a um departamento e uma turma possui um número, um período, um professor, um horário, o número de vagas ocupadas e o total de vagas e está assossiada a uma disciplina e a um departamento. 

### 2.1 Modelo Relacional

Na mesma pasta, podemos visualizar o modelo relacional do banco de dados (modelo_relacional.png) que apresenta a modelagem dos dados discutida anteriormente. Entretanto, farei algumas observações:

- Para cada tipo de dado, temos um atributo "id", o qual é a primary key de cada tabela;
- Todas as tabelas começam com letra maiúscula e não possuem caracteres especiais;
- Os atributos contém apenas letras minúsculas e seus nomes são formados com as três primeiras letras da tabela seguido do nome do atributo separado por "\_".
- Todas as relações são de um para n;
- Para diferenciar um usuário comum de um administrador, será o usado o atributo "est_adm" em vez de criar uma tabela para armazenar os administradores, uma vez que os administradores são estudantes da UnB;
- O modelo foi feito usando a ferramenta drawSQL.

## 3. Configuração do repositório

Conforme o projeto for evoluindo, novas configurações serão adicionadas e outras serão modificadas. Entretanto, os passos tomandos nessa etapa são necessários para que comecemos o desenvolvimento.

### 3.1 Pelo amor de deus

Antes de começarmos a configurar o projeto, certifique-se de já ter realizado os seguintes passos:

- Criar uma conta no GitHub;
- Instalar o WSL;
- Instalar o NodeJS;
- Instalar o Postgresql.

Além disso, utilizarei o WSL para realizar o projeto. Então, caso você deseje realizar o projeto no Windows, você terá que pesquisar diversas coisas extras.

### 3.2 Criando o repositório

Para criar o repositório no GitHub, faça login no site, crie um repositório e copie a url para clonar o repositório localmente. Em seguida, abra um terminal no WSL, navegue para o diretório que você deseje armazenar o repositório e digite o comando `git clone (copie a url do seu repositório aqui)`. No meu caso, naveguei para uma pasta chamada "projects", na qual eu armazeno meus projetos, e em seguida clonei o repositório.

```
cd projects
git clone https://github.com/GMTonnera/avaliacao-unb.git
```

Em seguida, navegue para o repositório clonado e abra o editor de texto de sua escolha. No caso, utilizarei o VS Code.

```
cd avaliacao-unb
code .
```

### 3.3 Criando o arquivo "package.json"

Com o VS Code aberto, agora criarei o arquivo "package.json" no repositório para poder instalar as dependências do projeto. Para isso, abri um terminal WSL no VS Code e digitei o comando `npm init`. Após preencher as informações, o arquivo "package.json" será criado.

### 3.4 Baixando dependências do projeto

Para baixar pacotes npm, basta digitar o comando `npm install (nome do pacote)`. No nosso projeto, utilizaremos TypeScript, uma extenção da linguagem JavaScript, a qual nos permite tipar variáveis, argumentos e retorno de funções. Assim, digitei o comando `npm install typescript --save-dev` no terminal. Ao rodar o comando, percebe-se que apareceu o arquivo "package-lock.json" e uma pasta chamada "node_modules". O arquivo contém informações sobre as dependências do projeto e a pasta armazena essas dependências.

Com o typescrpit instalado, podemos usar o comando `npx tsc (arquivo ts)` para compilar um arquivo .ts, gerando um arquivo .js. Porém, para gerenciar as configurações do compilador TypeScript (tsc) precisamos de um arquivo chamado "tsconfig.json". Para gerar esse arquivo, precisamos rodar o comando `npx tsc --init`. Rodando esse comando, o arquivo será criado e terá o sgeuinte conteúdo:
```
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```

### 3.5 Configurações do TSC

Para melhorar a organização do repositório, faremos algumas alterações no arquivo "tsconfig.json". 
```
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./js_files",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

Essas alterações farão com que, quando pedirmos para o tsc compilar o projeto, ele compile os arquivos da pasta "src" e coloque os arquivos .js na pasta "js_files".

### 3.6 Pastas

Para também melhorar a organização do projeto, criaremos a pasta "src" para armazenar os arquivos .ts do nosso projeto e a pasta "js_files" para armazenar os arquivos .js resultantes da compilação.

### 3.7 Arquivo .gitignore

Nesse momento, antes de seguir para o desenvolvimento, é interessante realizar um primeiro commit com as configurações inicias do projeto. Porém, se realizarmos esse commit agora, a pasta "node_modules" será carregada no repositório do GitHub, porém isso deixaria o repositório extremamente pesado. Para contornar essa situação, precisamos de um arquivo .gitignore.

Um arquivo .gitignore é responsável por dizer ao git quais arquivos ignorar ao realizar um commit. Dessa maneira, conseguimos evitar que os pacotes npm sejam carregados no repositório do GitHub. Então, devemos criar esse arquivo ".gitignore" e colocar o seguinte conteúdo nele:
```
node_modules
js_files
```
Assim, nenhum arquivo das pastas "node_modules" e "js_files" serão enviados no nosso commit inicial. Agora, basta realizar o commit:
```
git add .
git commit -m "configurações iniciais"
```
## 4. Início do desenvolvimento

Agora que temos um repositório configurado com TypeScript, podemos finalmente iniciar o desenvolvimento do nosso projeto. Obviamente, poderiamos desenvolver o sistema na ordem em que bem entendessemos. Porém, em um projeto real, os primeiros elementos desenvolvidos de um projeto são aqueles que mais agregam ao cliente, ou seja, aquele requisitos minímos que precisam ser implementados para o sistema possa ser usado pelos usuário. 

Então, em vez de desenvolver o sistema de camada em camada, faremos um desenvolvimento pensando nos serviços mais importantes a serem implementados. No nosso caso, precisamos fazer com que os estudantes da UnB realizem avaliações das turmas. Então, nada mais justo que começar desenvolvendo as partes relacionadas com os usuário. Assim, eles já poderam criar um cadastro no nosso sistema.

### 5. PrismaORM

Primeiramente, precisamos criar e configurar
Para implementar o banco de dados, faremos o uso do ORM (Oriented Relational Mapping) Prisma. Um ORM é uma ferramenta que gera código SQL a partir de uma linguagem mais simples. Dessa forma, muitas tarefas já são automatizadas, o que acelera o desenvolvimento do sistema. Além disso, essa ferramenta é usada nos projetos reais da CJR.

