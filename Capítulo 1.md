# Bancos de dados e SQL

Bancos de dados relacionais

Estabelecem relações específicas entre duas ou mais tabelas.

```
Tabelas com dados de clientes.
    Tabelas com detalhes vinculados aos pedidos de compras.
        Tabelas com os aspectos relevantes associados a cada item vendido.
```

Dados relacionais, em geral, são geridos por `SQL`.


Bancos de dados não-relacionais

Tudo aquilo que não é alocado em tabelas (gráficos, vídeos, áudios, imagens). Dados não relacionais são tratados pelo `NoSQL` (<i>Not only SQL</i>)

> <b>Pesquisa:</b>
> * Data literacy


Tipos de bancos de dados

Oracle, SQL Server, MySQL, PostgreSQL, MongoDB


# Beginning SQL Queries

## Capítulo 1, Visão Geral de Bases de Dados Relacionais

Diferentes abordagens de consulta para que esta possa ser expressa corretamente em SQL.

Objetivos de estudo

* Rever ideias e termonologias associadas a base de dados relacionais;
* Estudar modelos de dados (representa como uma base de ddados é montada, ou seja, quais dados são armazenados e como tudo está interrelacionado).

A base de dados deve ter sido desenhada para representar precisamente a situação com a qual está lidando. Sobre o <i>desenho de base de dados</i>, ver o livro <i>Beginning Database Design: From Novice to Professional</i>.

> <b>Pesquisa:</b>
> * <i>Beginning Database Design: From Novice to Professional</i>


### Introdução a Tabelas de Bases de Dados

Uma base de dados é um conjunto de tabelas. Nos tópicos a seguir, a maioria dos exemplos será baseada numa base de dados de um clube de golfe. <br><br>


<b>Atributos</b><br>

São unidades de informações da tabela. Na tabela `Member` contém informações sobre `nome`, `endereço` e `contato`. Para definir um atributo, devemos fornecer um nome, que pode ser `FamilyName`, e um domínio ou tipo. O domínio de um atributo pode ter valor de texto, número inteiro ou data, por exemplo. Os nomes dos atributos aparecerão no cabeçalho de suas respectivas colunas.<br><br>


<b>Chave primária</b><br>

É o meio de manter a unicidade de linhas, isto é, evitar que haja duas linhas com valores iguais em todas as colunas. No caso da tabela `Member` do nosso exemplo, a chave primária é `MemberID`. A chave primária também tem a vantagem de ajudar a mapear observações que tenham seus valores alterados. Uma chave primária garante que poderemos encontrar uma única linha na tabela para uma determinada observação. Isso é importante na hora de determinar relações entre tabelas.<br><br>

O código a seguir mostra o código em SQL que cria a tabela `Member`:

```SQL
CREATE TABLE Member (
    MemberID INT PRIMARY KEY,
    LastName CHAR(20),
    FirstName CHAR(20),
    Handicap INT,
    JoinDate DATETIME,
    Gender CHAR(1)
);
```

Em SQL, `INT` significa número inteiro ou número não-fracionário e `CHAR(n)` significa uma string com dimensão de n caracteres. O código também especifica que o atributo `MemberID` é uma chave primária com valor inteiro por meio do comando `INT PRIMARY KEY`.<br><br>


<b>Inserindo e atualizando linhas na tabela</b><br>

O código a seguir insere uma linha inteira em `Member`:

```SQL
INSERT INTO MEMBER
VALUES (
    118,
    'McKenzie',
    'Melissa',
    '963270',
    30,
    '05/10/1999',
    'F'
)
```

Se houver itens vazios, é possível especificar quais atributos terão valores:

```SQL
INSERT INTO Member (
    MemberID, LastName
)

VALUES (
    258, 'Olson'
)
```

É possível atualizar valores que já foram adicionados à tabela. A query a seguir encontra a linha onde `MemberID` é igual a `118` e altera o valor do atributo `Phone`:

```SQL
UPDATE Member
SET Phone = '875077'
WHERE MemberID = 118
```

Essa query especifica com `WHERE` quais linhas devem ser alteradas e também especifica quais campos devem ser alterados com o comando `SET`.


### Introdução a Modelos de Dados

Um modelo de dados é um conceito de modelo de dados subjacentes e como eles estão relacionados. Aqui, usaremos a notação de diagramas de classes da Linguagem Unificada de Modelagem (<i>UML - Unified Modeling Language</i>) para representar nossos modelos de dados.<br><br>

Agora, veremos como interpretar um diagrama de classe e como traduzí-lo em tabelas e restrições numa base de dados relacional.<br><br>

Uma classe é um modelo de algo sobre o que queremos guardar dados. Por exemplo, a tabela `Member` é uma classe com os atributos `MemberID`, `LastName`, `FirstName`, `Handicap`, `JoinDate`, `Gender`.

Classes diferentes podem se relacionar entre si. Suponhamos que cada membro pertença a um time e que haja uma tabela `Team` com os atributos `TeamName` e `PracticeNight`. Poderíamos traçar uma linha ligando os diagramas das classes estabelecendo uma relação <i>plays for</i>.<br><br>

As pontas dessa linha apresenta um par de números. Na ponta próxima à classe `Member`, poderíamos ter o par `0:n`. Isso significa que se lermos a relação partindo da classe `Team` para a classe `Member`, então leremos como <i>um time pode conter zero ou n membros</i>. <br><br>

Na ponta da linha que fica próxima à classe `Team`, poderiamos ter o par `0:1`. Iniciando a leitura da classe `Member` até a classe `Team`, leremos que <i>um membro pode fazer parte de zero ou um time</i>.<br><br>

Como um time pode ter `n` membros, podemos usar a chave primária da classe `Team`, que é o atributo `TeamName` para criar um novo atributo `Team` na classe `Member`. A coluna `Team` adicionada é chamada <i>foreign key</i>. O código abaixo mostra uma query SQL que cria uma tabela com uma foreign key:

```SQL
CREATE TABLE Member (
    MemberID INT PRIMARY KEY,
    LastName CHAR(20),
    FirstName CHAR(20),
    Phone CHAR(20),
    Handicap INT,
    JoinDate DATETIME,
    Gender CHAR(1)
    Team CHAR(20) FOREIGN KEY REFERENCES Team
);
```

Cada valor não vazio em `Member` deve ser um valor que exista em `Team`. Isso cria uma restrição chamada <i>referential integrity</i> que faz com que não atribuamos membros a times que não existem.


### Extraindo Informações de uma Base de Dados

Agora que sabemos como funciona uma base de dados com tableas interrelacionadas e normalizadas, veremos agora como extrair informações desta base. A query abaixo extrai os nomes de membros sêniores da tabela `Member`:

```SQL
SELECT Lastname, FirstName
FROM Member
WHERE MemberType = 'Senior';
```

Nesta consulta usamos `SELECT` para expecificar a coluna de onde as informações serão extraídas, `FROM` para especificar em que tabela estão as colunas e `WHERE` para especificar as condições que as linhas retornadas devem obedecer.



Suponhamos que queremos extrair uma lista de membros que treinam na segunda-feira. Podemos primeiro acessar a tabela `Team` e extrair as linhas que contenham o valor `Monday` na coluna `PracticeNight`. Em seguida, podemos juntar estas linhas à tabela `Member` e então extrair os nomes do resultado. <i>process approach</i>.

Uma outra maneira de executar a mesma tarefa é retornar as linhas de `Member` para membros que estão em times que treinam na segunda-feira.
