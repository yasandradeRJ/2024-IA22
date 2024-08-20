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

Clique com o botão diereiro do mouse no numero da porta (3333), vai aparecer a opção **"visibilidade da porta"**, troque a opção de *privado* para *publico*.

Agora clique no link que aparece ao lado do numero da porta, e é esse link que voce deve colar aonde em todos os lugares que esta escrito ">>SEU LINK<<".

obs: no final do link deve ter o */users*.
````javascript
POST https://seusite/users HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}


````
Vai aparecer um *"Enviar solicitação"*na primeira linha, no POST. 
Se for tudo certinho, quando voce abrir o link da primeira linha vai aparecer o seguinte:
````javascript
{
  "id": 1,
  "name": "John Doe",
  "email": "
}

````
# Aprendendo mais um pouco de BD 👇👇
Alem do comando "Post" que envia os dados pro servidor, tem o "Update" que atualiza, e o "Delete" que apaga, os dados.

# Atualizando o teste.http
Deve ficar igual o a baixo.
obs: lembre que pode so copiar e colar 😁
````javascript
POST (>>>>>>>>>>SEU LINK /users<<<<<<<<<<) HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}
####

PUT  (>>>>>>>>>>SEU LINK /users/1<<<<<<<<<<) HTTP/1.1
Content-Type: application/json

{
  "name": "John Doe update",
  "email": "john@example.com"
}

####

DELETE  (>>>>>>>>>>SEU LINK /users/1<<<<<<<<<<) HTTP/1.1


````
Modifique tambem o **"app.ts"**:
````javascript
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});

app.put('/users/:id', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;
  const { id } = req.params;

  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

  res.json(user);
});

app.delete('/users/:id', async (req, res) => {
  const db = await connect();
  const { id } = req.params;

  await db.run('DELETE FROM users WHERE id = ?', [id]);

  res.json({ message: 'User deleted' });
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});

````
# Estilizando a página
Crie uma pasta nomeando-a de *public*, e um arquivo dentro dela, chamando de *index.html*


Dentro deste arquivo coloque o código:
````javascript
<!DOCTYPE html>
<html lang="pt-BR">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="index.css">
  <title>Grey's Anatomy</title>
</head>

<body>
  <div class="container">
    <h1>Grey's Anatomy</h1>
    <h3>Petição contra o fim da série</h3>

    <form>
      <input type="text" name="name" placeholder="Nome" required>
      <input type="email" name="email" placeholder="Email" required>
      <button type="submit">Cadastrar</button>
    </form>

    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Nome</th>
          <th>Email</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <!--  -->
      </tbody>
    </table>
  </div>

  <script>
    const form = document.querySelector('form')

    form.addEventListener('submit', async (event) => {
      event.preventDefault()

      const name = form.name.value
      const email = form.email.value

      await fetch('/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email })
      })

      form.reset()
      fetchData()
    })

    const tbody = document.querySelector('tbody')

    async function fetchData() {
      const resp = await fetch('/users')
      const data = await resp.json()

      tbody.innerHTML = ''

      data.forEach(user => {
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${user.id}</td>
          <td>${user.name}</td>
          <td>${user.email}</td>
          <td>
            <button class="excluir">Excluir</button>
            <button class="editar">Editar</button>
          </td>
        `

        const btExcluir = tr.querySelector('button.excluir')
        const btEditar = tr.querySelector('button.editar')

        btExcluir.addEventListener('click', async () => {
          await fetch(`/users/${user.id}`, { method: 'DELETE' })
          tr.remove()
        })

        btEditar.addEventListener('click', async () => {
          const name = prompt('Novo nome:', user.name)
          const email = prompt('Novo email:', user.email)

          await fetch(`/users/${user.id}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
          })

          fetchData()
        })

        tbody.appendChild(tr)
      })
    }

    fetchData()
  </script>
</body>

</html>

````

# Estilizando a página
Crie outro arquivo chamando ele de **"index.css"**, tambem dentro do *public*, e adicione o código: 

````javascript
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  
  body {
    background-image: url(https://raw.githubusercontent.com/Edson-Edu/2024-IA22-2TRI/main/public/seattle.jpg);
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    color: #fff;
    font-family: Arial, sans-serif;
    text-align: center;
    line-height: 1.5;
  }
  
  .container {
    background: rgba(0, 0, 0, 0.7); 
    border-radius: 10px;
    padding: 20px;
    max-width: 800px;
    margin: 50px auto;
  }
  
  h1 {
    font-size: 2.5em;
    margin-bottom: 10px;
  }
  
  h3 {
    font-size: 1.5em;
    margin-bottom: 20px;
  }
  
  form {
    margin-bottom: 20px;
  }
  
  form input[type="text"],
  form input[type="email"] {
    padding: 10px;
    border: none;
    border-radius: 5px;
    margin: 5px;
    width: calc(50% - 22px);
  }
  
  form button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    background-color: #007BFF;
    color: #fff;
    cursor: pointer;
    font-size: 1em;
  }
  
  form button:hover {
    background-color: #0056b3;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  table th, table td {
    padding: 10px;
    border: 1px solid #fff;
  }
  
  table th {
    background-color: rgba(0, 0, 0, 0.8);
  }
  
  table td {
    background-color: rgba(0, 0, 0, 0.6);
  }
  
  table button {
    padding: 5px 10px;
    border: none;
    border-radius: 5px;
    color: #fff;
    cursor: pointer;
    font-size: 0.9em;
    margin: 2px;
  }
  
  table button.excluir {
    background-color: #dc3545;
  }
  
  table button.excluir:hover {
    background-color: #c82333;
  }
  
  table button.editar {
    background-color: #28a745;
  }
  
  table button.editar:hover {
    background-color: #218838;
  }
  
````
# Mudando novamente o *app.ts*
A baixo da linha 9, que tem essa parte **app.use(express.json());** ponha essa parte:

````javascript
app.use(express.static(__dirname + '/../public'))

````
No final ele fica assim:
````javascript
import express from 'express'
import cors from 'cors'
import { connect } from './database'

const port = 3333
const app = express()

app.use(cors())
app.use(express.json())
app.use(express.static(__dirname + '/../public'))

app.get('/users', async (req, res) => {
  const db = await connect()
  const users = await db.all('SELECT * FROM users')
  res.json(users)
})

````
# Parabens! Finalizamos por aqui.

````javascript
agora acesse o site, e brinque com ele!!!!

````
