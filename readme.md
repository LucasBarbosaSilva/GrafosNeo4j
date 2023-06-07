# Neo4j - Banco Orientado a Grafos

Data de Criação: April 10, 2023 3:30 PM
Matéria: TG (https://www.notion.so/TG-be495255c4aa4c969bdbca451ea7a07c?pvs=21)
Property: P Atual

Por @Lucas Barbosa 

## Vídeo

[Configurando Neo4j no Docker, Modelagem e Consultas](https://youtu.be/K9ggfZfqAs0)

## Instalação e configuração

### Baixar a imagem oficial do Neoj4 do Docker

```bash
docker pull neo4j
```

### Criar o container na nossa máquina

```bash
docker run -d -p 7687:7687 -p 7474:7474 -v $(pwd)/data:/data -v $(pwd)/logs:/logs -v $(pwd)/import:/import -v $(pwd)/plugins:/plugins --env NEO4J_AUTH=neo4j/test1234 --name meu-neo4j neo4j
```

Desmebrando esse código:

- **docker run -d:** comando do docker para criar algum container a partir de uma imagem. A flag -d desanexa o container do seu terminal;
- **-p 7687:7687:**  mapeando a porta 7687 da máquina host, para a porta 7687 do container. Essa porta é usada pelas linguagens de programação;
- **-p 7474:7474:** mapeando a porta 7474, que será usada pelo Gerenciador Web do banco de dados;
- **-v $(pwd)/data:/data:** mapeando o diretório <path pro container no host>/data para o diretório /data do container. Lá será armazenado os dados;
- **-v $(pwd)/logs:/logs:** mapeando o diretório <path pro container no host>/logs para o diretório /logs do container. Lá será armazenado os logs;
- **-v $(pwd)/import:/import:** mapeando o diretório <path pro container no host>/import para o diretório /import do container. Será utilizado quando quiser enviar algum arquivo de importação pro container;
- **-v $(pwd)/plugins:/plugins:** mapeando o diretório <path pro container no host>/plugin para o diretório /plugin do container. Será utilizado quando quiser adicionar algum plugin no container;
- **--env NEO4J_AUTH=neo4j/test1234:** setando a variável de ambiente NEO4J_AUTH, que trata da autenticação do banco. (usuário)/(senha);
- **--name meu-neo4j:** nome do container;
- **neo4j:** o nome da imagem que está na nossa máquina instalada.

### Acessando o gerenciador web:

[localhost:7474](http://localhost:7474/)

### Conectando com o banco de dados:

Passar as credenciais.

## Modelagem do problema

### Grafo:

![SEGUIR (3).png](Neo4j%20-%20Banco%20Orientado%20a%20Grafos%20b26d4f2d542b49729afaf98e51051693/SEGUIR_(3).png)

Propriedades de cada pessoa:

- Joana:
    - Idade: 18 anos
    - Trabalho: motorista
- Marquinhos
    - Idade: 29 anos
    - Trabalho: desempregado
    - Conjuge: Regina
- Regina:
    - Idade: 34 anos
    - Trabalho: cozinheira
    - Conjuge: Marquinhos
- Júnior:
    - Idade: 17 anos
- Amanda:
    - Idade: 20 anos
    - Hooby: tocar teclado

Nó: ()

Relacionamento: []

Propriedade: {}

### Inserção dos nós no banco:

```bash
create (:Pessoa {nome: "Joana", idade: 18, trabalho: "Motorista"})
create (:Pessoa {nome: "Marquinhos",  idade: 29, trabalho: "Desempregado"})
create (:Pessoa {nome: "Regina",idade: 34, trabalho: "Cozinheira", conjuge: "Marquinhos"})
create (:Pessoa {nome: "Júnior", idade: 17})
create (:Pessoa {nome: "Amanda", idade: 20, hobby: "Tocar teclado"})
```

### Inserção dos relacionamentos:

```bash
match (joa:Pessoa where joa.nome = "Joana")
match (mar:Pessoa { nome: "Marquinhos"})
match (reg:Pessoa { nome: "Regina"})
match (jun:Pessoa { nome: "Júnior"})
match (ama:Pessoa { nome: "Amanda"})

create (joa)-[:SEGUIR]->(reg)
create (joa)<-[:SEGUIR]-(reg)
create (reg)-[:ODIAR]->(jun)
create (reg)<-[:ODIAR]-(jun)
create (joa)<-[:SEGUIR]-(mar)
create (mar)-[:SEGUIR]->(reg)
create (mar)-[:ODIAR]->(jun)
create (mar)-[:SEGUIR]->(ama)
create (ama)-[:ODIAR]->(joa)
create (ama)-[:ODIAR]->(mar)
create (ama)-[:ODIAR]->(reg)
create (ama)-[:ODIAR]->(jun)
create (jun)-[:SEGUIR]->(mar)
```

### Visualizando o grafo:

```bash
match (n) return n
```

![graph.png](Neo4j%20-%20Banco%20Orientado%20a%20Grafos%20b26d4f2d542b49729afaf98e51051693/graph.png)

## Consultas

- Nome das pessoas cadastradas

```bash
match (p:Pessoa) return p.nome
```

- Quantidade de relacionamentos

```bash
match ()-[r]->() return count(r)
match ()-[r:ODIAR]->() return count(r)
match ()-[r:SEGUIR]->() return count(r)
```

- Quantas pessoas seguem Joana

```bash
match (p:Pessoa)<-[:SEGUIR]-(joa:Pessoa {nome: "Joana"}) return count(p)
```

- Quantas Pessoas Joana segue

```bash
match (p:Pessoa)<-[:ODIAR]-(joa:Pessoa {nome: "Joana"}) return count(p)
```

- Quem Joana segue

```bash
match (joa:Pessoa {nome: "Joana"})-[:SEGUIR]->(p:Pessoa) return p.nome
```

- Quantas pessoas Amanda odeia e quem são elas

```bash
match (ama:Pessoa {nome: "Amanda"})-[:ODIAR]->(p:Pessoa) return count(p)
```

```bash
match (ama:Pessoa {nome: "Amanda"})-[:ODIAR]->(p:Pessoa) return p.nome
```

- Deletar um relacionamento

```bash
match (ama:Pessoa {nome: "Amanda"})-[odi:ODIAR]->(mar:Pessoa {nome: "Marquinhos"}) delete odi
create (ama)-[:SEGUIR]->(mar)
```

- Reconfigurar uma propriedade

```bash
match (mar:Pessoa {nome: "Marquinhos"})
set mar.trabalho = "Professor da UFAL"
```

- Deletar nó pelo id do Neo4j

```bash
match (jun:Pessoa where ID(jun)= 3) detach delete jun
```

## Materiais de referência

[Docker - NoSQL - Neo4J](https://www.youtube.com/watch?v=WCjh-Es4UAg)

[Docker em 22 minutos - teoria e prática (Rápido!)](https://www.youtube.com/watch?v=Kzcz-EVKBEQ)

[Docker em 22 minutos - teoria e prática (Rápido!)](https://www.youtube.com/watch?v=Kzcz-EVKBEQ)