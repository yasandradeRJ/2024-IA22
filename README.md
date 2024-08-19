# 2024-IA22

**Programação II**

Bora começar o primeiro servidor com rotas top das galáxias!

# Iniciando um repositório no CodeSpace
Crie um "repositório" nomeando com "(seu nome)-IA24".
No seu git hub, na parte de cima do canto direito, tem uma caixinha com um "+"(mais) dentro, aperte la, e depois em `"new repository"`.

*Lembre: o repositório deve ser com o arquivo README incluso.

Agora, clique no botão `code` e depois em `codespace on main`, espere aparecer o "gatinho" do github para continuar os próximos passos 


# Iniciando um projeto **Node.js** com TypeScript
La no terminal digite os comandos a baixo, execute eles um por vez (enter) esperando o tempo certo de carregamento, se voce nao quiser que tenha problemas.

````javascript
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
touch src/app.ts

````
# Configurando o **tsconfig.json**
Usando o atalho "Control + F", pesquise ´´"outDir": "./"´´ mude essa linha para ´´"outDir": "./dist"´´ e adicione embaixo  "rootDir": "./src". No final ele vai ficar assim:

````javascript
{
  "compilerOptions": {
    "target": "ES2017",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}

````
 
# Configurando o **package.jon**
Dentro do "scripts" que esta no package.jon (lembre que voce pode usar "Ctrl F" para pesquisar) adicione a seguinte linha:

````javascript
  "dev": "npx nodemon src/app.ts"

````
No final fica assim: 
````javascript
   "scripts": {
    "test": "echo \"Error: no test 
     specified\" && exit 1",
    "dev": "npx nodemon src/app.ts"
  },

````
# Criando arquivo inicial do servidor
Na pasta "src" tem o arquivo "app.ts", la dentro adicione esse código:

````javascript
import express from 'express';
import cors from 'cors';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});

````
# Iniciando o Servidor
Antes de tudo, voce deve instalar a biblioteca **"REST Client"**, no quinto icone a esquerda (formato de um cubo quebrado), pesquise o nome a cima, e aperte o botão "instalar". Finalizando essa parte, vamos para a próxima.

Para poder ver a sua pagina, digite o seguinte comando no terminal:

````javascript
npm run dev

````
obs: Antes de tudo, quando voce finalizar a ação a baixo, aparecera uma mensagem com a opção ´´Abrir no navegador´´, aperte nela

*voltando; Se funcionar vai aparecer a mensagem: "running on port 3333" no terminal.

Quando voce abrir o navegador, vai aparecer a mensagem "Hello World".


# Configurando Banco de dados
Dentro da pasta "src" crie um arquivo nomeando "database.ts" e ponha esse código: 

````javascript
import { Database, open } from 'sqlite';
import sqlite3 from 'sqlite3';

let instance: Database | null = null;

export async function connect() {
  if (instance) return instance;

  const db = await open({
     filename: './src/database.sqlite',
     driver: sqlite3.Database
   });
  
  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      email TEXT
    )
  `);

  instance = db;
  return db;
}

````

# Testando a inserção de dados
Saia da pasta "src" e crie um arquivo chamado "teste.http" e adicione o código: 
obs: ponha o **seu link**, a baixo tera a explicação de como fazer isso.

````javascript
POST (>>>>>>>>>>SEU LINK /users<<<<<<<<<<) HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}


````
No terminal, ao lado de "Terminal" (caso sua página esteja menor, tera 3 pontinhos), se não apenas a opção "Portas".

Clique nela, e então aparecera a sua porta, com o endereço e a visibilidade.



# Titulo
Aqui fica o textinho

````javascript
Y

````


`destaca`
**destaca2**