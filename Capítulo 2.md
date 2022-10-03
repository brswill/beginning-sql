# Beginning SQL Queries

## Capítulo 2, Queries Simples em uma Tabela

Nossa base de dados sobre golfe tem tabelas separadas para membros, times e torneios. E também há tabelas que conectam esses valores.<br><br>

Veremos agora como extrair informação de uma única tabela. Uma query, ou uma consulta, é como uma janela dentro da base de dados onde podemos ver apenas a informação que queremos.


### Subconjuntos de Linhas e Colunas

As operações mais comuns em uma query são as seleções de subconjuntos de linhas e/ou colunas. Em termos da Álgebra Relacional, extrair um subconjunto de linhas (tuplas) de uma tabela (relação) é uma operação chamada <i>select</i> e extrair um subconjunto de colunas (atributos) é uma operação chamada <i>project</i>.<br><br>

Para determinar quais linhas queremos extrair, devemos especificar uma condição, que é uma afirmação de verdadeiro ou falso. Dada a condição, serão retornadas apenas as linhas para as quais a condição é verdadeira. Suponha que queiramos saber quais são os membros seniores do clube de golfe. Para isso, devemos usar a seguinte query em SQL:

```SQL
SELECT *
FROM Member
WHERE MemberType = 'Senior'
```

A query acima usa a cláusula `SELECT *` para selecionar todas as colunas. Depois usa a cláusula `FROM` para especificar que serão selecionadas todas as colunas da tabela `Member`. E, em seguida usa a cláusula `WHERE` que especifica uma condição. A condição é a de que serão selecionadas as linhas que tenham o valor da coluna `MemberType` igual a `Senior`.<br><br>

Agora, suponha que queiramos extrair informações de nome, sobrenome e telefone de todos os membros. Para isso, faremos a seguinte consulta:

```SQL
SELECT LastName, FirstName, Phone
FROM Member
```

Nesse caso, usamos a cláusula `SELECT` seguida dos nomes das colunas que queríamos extrair. E, depois, usamos `FROM` para especificar a tabela. A query a seguir mostra como fazer as duas coisas, selecionar apenas nome, sobrenome e telefone dos membros seniores:

```SQL
SELECT LastName, FirstName, Phone
FROM Member
WHERE MemberType = 'Senior'
```


### Usando 'Apelidos'

Queries mais complexas exigem lidar com diferentes tabelas. Algumas dessas tabelas podem ter colunas com o mesmo nome e será necessário distinguí-las. Em SQL, podemos preceder o nome do atributo que estamos selcionando com o nome da tabela, como na query a seguir:

```SQL
SELECT Member.LastName, Member.FirstName, Member.Phone
FROM Member
WHERE Member.Member.MemberType = 'Senior'
```

Nesse caso, escrever sempre o nome da tabela pode ser cansativo. Daí, o SQL tem a noção de <i>apelidos</i>:

```SQL
SELECT m.LastName, m.FirstName, m.Phone
FROM Member m
WHERE m.MemberType = 'Senior'
```

Aqui, na cláusula `FROM`, declaramos um nome alternativo para a tabela `Member`, que é `m` e será usado no restante da query.


### Salvando as Consultas

Em SQL, um <i>snapshot</i> é o que fazemos quando salvamos o resultado de uma query em uma tabela nova e permanente. Em geral, não é isso o que queremos fazer, mas sim salvar as instruções que levaram àquele resultado. Isto é, suponha que extraímos a lista de nomes, sobrenomes e telefones de membros. Suponha que depois disso, a lista de membros seja atualizada. Isso fará com que precisemos extrair uma nova lista do mesmo tipo.<br><br>

Em SQL, uma <i>view</i> é o que fazemos quando salvamos as instruções de uma query feita. Em nosso exemplo, declaramos o seguinte:

```SQL
CREATE VIEW PhoneList AS
SELECT m.LastName, m.FirstName, m.Phone
FROM Member m
```

Para acessar a lista de telefones novamente, basta usar

```SQL
SELECT *
FROM PhoneList
```


### Especificando Condições para Selecionar Linhas

Nesta seção, veremos como usar os operadores do SQL.

<b>Operadores Comparativos</b><br>

|Operador|Significado|
|:---:|:---:|
|`=`|Igual a|
|`<`|Menor que|
|`<=`|Menor ou igual a|
|`>`|Maior que|
|`>=`|Maior ou igual a|
|`<>`|Diferente|


<b>Operadores Lógicos</b><br>

Operadores lógicos combinam operadores comparativos. Observe a seguinte query:

```SQL
SELECT *
FROM Member m
WHERE m.MemberType = 'Junior' AND m.Gender = 'F'
```

Essa query extrai da tabela `Member` as linhas que tem valor `Junior` de atributo `MemberType` e que ao mesmo tempo têm valor `F` de atributo `Gender`. Isto é, as linhas para as quais as duas condições são verdadeiras ao mesmo tempo.

|Expressão|Descrição da Informação|
|:---:|:---:|
|`MemberType = 'Senior' AND Handicap < 12`|Seniores com valor abaixo de 12 no atributo `Handicap`|
|`MemberType = 'Senior' AND Handicap < 12`|Todos os membros seniores assim como qualquer um com valor abaixo de 12 no atributo `Handicap`|
|`NOT (MemberType = 'Social')`|Todos os membros exceto os que têm valor igual a `Social` no atributo `MemberType`|


### Lidando com Nulls

A query abaixo restringe, na criação da tabela, que o atributo `Gender` tenha algum valor.

```SQL
CREATE TABLE Member (
    MemberID INT PRIMARY KEY,
    ...
    Gender CHAR(1) NOT NULL,
    ...
)
```

<b>Encontrando Nulls</b>

Aqui, estamos selecionando as linhas sem nenhum valor na coluna `Gender`:

```SQL
SELECT *
FROM Member m
WHERE m.Gender IS NULL
```

E aqui, somente as linhas que tenham algum valor na coluna `Handicap`:

```SQL
SELECT *
FROM Member m
WHERE NOT (m.Handicap IS NULL)
```

<b>Comparações Envolvendo Valores Null</b>

```SQL
Gender = 'F'
NOT (Gender = 'F')
```


### Administrando Duplicidades

```SQL
SELECT DISTINCT m.MemberType
FROM Member m
```

Usamos a palavra-chave `DISTINCT` para extrair apenas valores únicos na consulta, que pode ter sido feita extraindo apenas uma coluna de nomes, como a coluna `m.MemberType`.


### Ordering Outputs

Uma consulta que extraia um conjunto de linhas ou todas as linhas não garante a ordem em que as linhas retornarão. A cláusula `ORDER BY` ordena o retorno de uma consulta. A consulta a seguir extrai todas as colunas da tabela `Member` com o nome alternativo `m` e ordena as linhas pelos valores da coluna `LastName`.

```SQL
SELECT *
FROM Member m
ORDER BY m.LastName
```

É possível ainda ordenar por mais de um valor. A consulta a seguir extrai todas as colunas da tabela para as linhas onde o valor da coluna `MemberType` é igual a `Senior` e, em seguida, ordena o retorno pela coluna `FirstName` para as linhas com mesmo valor em `LastName`.

```SQL
SELECT *
FROM Member m
WHERE m.MemberType = 'Senior'
ORDER BY m.LastName, m.FirstName
```

O tipo de campo importa na ordenação: texto é ordenado alfabeticamente, número é ordenado a partir do menor e datas e horas são ordenados a partir dos valores mais antigos. As cláusulas `DESC` e `ASC` são usadas para determinar a ordem. A consulta a seguir ordena o retorno pela coluna `Handicap`, começando pelo maior valor.

```SQL
SELECT m.LastName, m.FirstName, m.Handicap
FROM Member m
ORDER BY m.Handicap DESC
```

A ordenação pode trazer valores null no topo ou no final do retorno a depender da aplicação. Nesse caso, a cláusula `CASE` pode ajudar. A consulta a seguir extrai as colunas `LastName`, `FirstName` e `Handicap` da tabela `Member` com o nome alternativo `m`. Em seguida, usa-se a cláusula `CASE` para criar uma coluna em que as linhas com valor null na coluna `Handicap` terão valor 1 e as linhas não nulas terão valor 0. Só então, a consulta é ordenada pela condição entre parênteses.

```SQL
SELECT m.LastName, m.FirstName, m.Handicap
FROM Member m
ORDER BY (
    CASE
    WHEN m.Handicap IS NULL THEN 1
    ELSE 0
    END
), m.Handicap
```


### Performing Simple Counts

Existem funções em SQL que nos permitem fazer contagens, saber valor total, valor médio, mínimo e máximo de um subconjunto de uma tabela.

A função `COUNT` retorna o número de registros. A consulta abaixo usa o parâmetro `*` e retorna a contagem de todas as linahs da tabela. Essa contagem inclui valores null e duplicidades.

```SQL
SELECT COUNT(*) FROM Member
```

Um meio de lidar com duplicidades é usar o nome de uma coluna como parâmetro. A consulta abaixo retorna a contagem das linhas que contém algum valor na coluna `Handicap`.

```SQL
SELECT COUNT(Handicap) FROM Member
```

Para lidar com duplicidades, devemos incluir a cláusula `DISTINCT` nos parâmetros:

```SQL
SELECT COUNT(DISTINCT MemberType) FROM Member
```

É possível ainda fazer uma contagem de um subconjunto de uma tabela com a cláusula `WHERE`:

```SQL
SELECT COUNT(*) FROM Member m
WHERE m.MemberType = 'Senior'
```


## Lidando com Erros Comuns

Supondo as tabelas `Entry`e `Tournament`, que registram, respectivamente, os torneios de que cada membro participou e a lista de torneios, talvez queiramos responder a pergunta sobre quais membros participaram do torneio 36 em 2015. A consulta que traria essa resposta seria:

```SQL
SELECT e.MemberID
FROM Entry e
WHERE e.TourID = 36 AND e.Year = 2015
```

Agora, supondo que precisássemos responder quais membros participaram de ambos os torneios 36 e 38, a seguinte consulta

```SQL
SELECT e.MemberID
FROM Entry e
WHERE e.TourID = 36 AND e.TourID = 38
```

estaria errada.

Isso, porque a cláusula `AND` requer que uma linha satisfaça ambas as condições ao mesmo tempo, o que nesse caso é impossível, pois cada linha contém apenas um valor de `TourID`. Além disso, a cláusula `WHERE` se aplica a qualquer linha, isto é, seriam retornadas também as linhas com membros que tivesse participado apenas de um dos dois torneios.

Suponhamos agora que precisamos encontrar os membros que não participaram do torneio 38. A seguinte consulta também estaria errada:

```SQL
SELECT e.MemberID
FROM Entry e
WHERE e.TourID <> 38
```

Essa consulta retorna as linhas onde `TourID` é diferente de 38, mas isso não implica que os membro que aparecem nessa consulta nunca participaram do torneio 38. O que queremos de fato é encontrar os membros que nunca participaram desse torneio.

Em capítulos seguintes veremos as soluções para este problema e o anterior.