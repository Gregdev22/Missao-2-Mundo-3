<!-- PROJECT LOGO -->
<div align="center">
   <a href="https://github.com/othneildrew/Best-README-Template">
      <img src="https://logodownload.org/wp-content/uploads/2014/12/estacio-logo-1-2048x1641.png" alt="estacio logo" width="80"                  height="80">
   </a>
    <h1 align="center"> Universidade Estácio de Sá </h1>
     <hr>
</div> 

* DESENVOLVIMENTO FULL STACK- TURMA 23.3 -9003
* Disciplina: RPG0015  - Vamos Manter as Informações?
* Semestre Letivo: 2023.2
* Repositorio Git: https://github.com/Gregdev22/Missao-2-Mundo-3

<hr>

* [EMERSON GREGORIO ALVES](https://github.com/Gregdev22) - MATRICULA: 2022.0908.4986
<hr>
 <h1 align="center"> Missão Prática | Nível 2 | Mundo 3 </h1>
 <h2 align="left" > Modelagem e implementação de um banco de dados simples, utilizando como base o SQL
Server. </h2> 
 <h3>Procedimento 1: Criando o Banco de Dados </h3>
 <h3>Procedimento 2: Alimentando a Base </h3>
 
 <hr>


 <h2> :clipboard: Objetivos da Prática </h2>

* Identificar os requisitos de um sistema e transformá-los no modelo adequado.
* Utilizar ferramentas de modelagem para bases de dados relacionais.
* Explorar a sintaxe SQL na criação das estruturas do banco (DDL).
* Explorar a sintaxe SQL na consulta e manipulação de dados (DML).
* No final do exercício, o aluno terá vivenciado a experiência de modelar a base de dados
para um sistema simples, além de implementá-la, através da sintaxe SQL, na plataforma
do SQL Server
<hr>

<h2> Códigos </h2>

[Procedimento 1: Criando o Banco de Dados](https://github.com/Gregdev22/Missao-2-Mundo-3/tree/main/Procedimento%201)

``` sql
create database Loja;

use Loja;

create table pessoa(
  idpessoa int NOT NULL ,
  nome varchar(255) NOT NULL  ,
  logradouro varchar(255) NOT  NULL  ,
  cidade varchar(255)NOT  NULL  ,
  estado char(2)NOT  NULL  ,
  telefone varchar(11)NOT  NULL  ,
  email varchar(255)NOT  NULL    ,
primary key(idpessoa));

create table pessoa_fisica (
  idpessoa_fisica int NOT NULL,
  cpf varchar(255)  NOT NULL,
  primary key (idpessoa_fisica),
  constraint idpessoa foreign key (idpessoa_fisica) references pessoa(idpessoa));

create table pessoa_juridica (
  idpessoa_juridica int NOT NULL,
  cnpj varchar(255)  NOT NULL,
  primary key (idpessoa_juridica),
  constraint idpessoa_juridica foreign key (idpessoa_juridica) references pessoa(idpessoa));

create table produto (
  idproduto int NOT NULL  ,
  nome varchar(255)  NOT NULL  ,
  quantidade varchar(255)  NOT NULL  ,
  preco_venda numeric(5,2)  NOT NULL    ,
primary key(idproduto));

create table usuario (
  idusuario int NOT NULL ,
  login varchar(255)   NOT NULL  ,
  senha varchar(255)   NOT NULL    ,
primary key(idusuario));

create table movimento (
  idmovimento int NOT NULL,
  Usuario_idUsuario int  NOT NULL  ,
  pessoa_idpessoa int  NOT NULL  ,
  produto_idproduto int  NOT NULL  ,
  quantidade int  NOT NULL  ,
  tipo char  NOT NULL  ,
  valorUnitario numeric(5,2)  NOT NULL    ,
primary key(idmovimento),
foreign key (Usuario_idUsuario) references usuario(idusuario),
foreign key (produto_idproduto) references produto(idproduto),
foreign key (pessoa_idpessoa) references pessoa(idpessoa));

create sequence seq_Pessoa
	as numeric
	start with 1
	increment by 1
	no cycle;
```

[Procedimento 2: Alimentando a Base](https://github.com/Gregdev22/Missao-2-Mundo-3/tree/main/Procedimento%202)
``` sql
use Loja;

insert into usuario
values (1, 'op1', 'op1'),(2, 'op2', 'op2');

insert into produto
values (1, 'Banana', 100, 5.00),(3, 'Laranja', 500, 2.00),(4, 'Manga', 800, 4.00);

insert into pessoa
values (NEXT VALUE FOR seq_Pessoa, 'Joao', 'Rua 12, cas 3, Quitanda', 
'Riacho do Sul', 'PA', '1111-1111','joao@riacho.com');

insert into pessoa
values (NEXT VALUE FOR seq_Pessoa, 'JJC', 'Rua 11, Centro', 
'Riacho do Norte', 'PA', '1212-1212','jjc@riacho.com');

insert into pessoa_fisica
values (1,'11111111111');

insert into pessoa_juridica
values (2,'22222222222222');

insert into movimento
values (1,1,1,1,20,'S',4.00),
(4,1,1,3,15,'S',2.00),
(5,2,1,3,10,'S',3.00),
(7,1,2,3,15,'E',5),
(8,1,2,4,20,'E',4.00);

-- Dados completos de pessoas físicas.
select *
from pessoa, pessoa_fisica
where pessoa.idpessoa = pessoa_fisica.idpessoa_fisica;

--Dados completos de pessoas jurídicas.
select *
from pessoa, pessoa_juridica
where pessoa.idpessoa = pessoa_juridica.idpessoa_juridica;

--Movimentações de entrada, com produto, fornecedor, quantidade, preço unitário e valor total.
select idmovimento, produto_idproduto, produto.nome as 'Produto',pessoa_idpessoa, pessoa.nome as 'Fornecedor', movimento.quantidade, valorUnitario,
(movimento.quantidade *  valorUnitario) as valor_total
from movimento
join pessoa
on movimento.pessoa_idpessoa = pessoa.idpessoa
join produto 
on movimento.produto_idproduto = produto.idproduto
where movimento.tipo = 'E';

--Movimentações de saída, com produto, comprador, quantidade, preço unitário e valor total
select idmovimento, produto_idproduto, produto.nome as 'Produto',pessoa_idpessoa, pessoa.nome as 'Comprador', movimento.quantidade, valorUnitario,
(movimento.quantidade * valorUnitario) as valor_total
from movimento
join pessoa
on movimento.pessoa_idpessoa = pessoa.idpessoa
join produto 
on movimento.produto_idproduto = produto.idproduto
where movimento.tipo = 'S';

--Valor total das entradas agrupadas por produto.
select produto.nome, SUM (movimento.quantidade * movimento.valorUnitario) AS 'VALOR TOTAL ENTRADAS'
from movimento
JOIN produto
on produto.idproduto = movimento.produto_idproduto 
where movimento.tipo = 'E'
group by produto.nome;

--Valor total das saídas agrupadas por produto.
select produto.nome, SUM (movimento.quantidade * movimento.valorUnitario) AS 'VALOR TOTAL SAIDAS'
from movimento
JOIN produto
on produto.idproduto = movimento.produto_idproduto 
where movimento.tipo = 'S'
group by produto.nome;

--Operadores que não efetuaram movimentações de entrada (compra).
select movimento.Usuario_idUsuario AS 'ID DO OPERADOR'
from movimento
except
select movimento.Usuario_idUsuario
from movimento
where movimento.tipo = 'E';

--Valor total de entrada, agrupado por operador.
select usuario.login AS OPERADOR, SUM (movimento.quantidade * movimento.valorUnitario) AS 'VALOR TOTAL ENTRADAS'
from movimento
JOIN usuario
on usuario.idusuario = movimento.Usuario_idUsuario 
where movimento.tipo = 'E'
group by usuario.login;

--Valor total de saída, agrupado por operador.
select usuario.login AS OPERADOR, SUM (movimento.quantidade * movimento.valorUnitario) AS 'VALOR TOTAL SAIDAS'
from movimento
JOIN usuario
on usuario.idusuario = movimento.Usuario_idUsuario 
where movimento.tipo = 'S'
group by usuario.login;

--Valor médio de venda por produto, utilizando média ponderada.
select produto.nome, SUM (movimento.quantidade * movimento.valorUnitario) / SUM(movimento.quantidade) as 'Valor médio de venda'
from movimento
JOIN produto
on produto.idproduto = movimento.produto_idproduto 
where movimento.tipo = 'S'
group by produto.nome;
```
<hr>

<h1>Resultados: </h1>
:triangular_flag_on_post: Procedimento 1: 
<h2>Modelo</h2>
 <img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%201/Modelo%20procedimento%201.png" alt="resultado 1" width="640" height="640">
<h2> Banco e Tabelas </h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%201/Proc%201%20tabelas.png" alt="resultado 1" width="640" height="480">
<h2> Usuario/Logon Loja </h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%201/Proc%201%203a.png" alt="resultado 1" width="640" height="480">
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%201/Proc%201%203b.png" alt="resultado 1" width="640" height="480">
<br>

:triangular_flag_on_post:Procedimento 2: 
<h2>Usuarios</h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%201b.png" alt="resultado 1" width="480" height="320">
<h2>Produtos</h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%201c.png" alt="resultado 1" width="480" height="320">
<h2>Movimentos</h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%203.png" alt="resultado 1" width="480" height="320">
<h2> Dados completos de pessoas físicas e juridicas </h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%202c%202d.png" alt="resultado 1" width="480" height="320">
<h2> Movimentações de entrada e saida, com produto, comprador, fornecedor, quantidade, preço unitário e valor total </h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%204c%204d.png" alt="resultado 1" width="640" height="320">
<h2> Valor total das entradas e saidas agrupadas por produto</h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%204e%204f.png" alt="resultado 1" width="640" height="320">
<h2> Operadores que não efetuaram movimentações de entrada (compra).Valor total de entrada e saida agrupado por operador </h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%204g%204h%204i.png" alt="resultado 1" width="640" height="320">
<h2> Valor médio de venda por produto, utilizando média ponderada </h2>
<img src="https://github.com/Gregdev22/Missao-2-Mundo-3/blob/main/Procedimento%202/Proc%202%204j.png" alt="resultado 1" width="640" height="320">
<hr>
<h1>Análise e Conclusão</h1>
<ul>
   <li>
      Quais as vantagens e desvantagens do uso de herança?
      <p> 
        <h4> <strong>Vantagens: </strong></h4>
         1. Classes podem herdar características(métodos e atributos) de outras classes situadas acima ou transmitir suas características às classes abaixo;
         <br>
         2. Evita repetir o mesmo código várias vezes;
         <br>
         3. Caso uma alteração seja necessária, ela só precisará ser feita na classe pai, e será automaticamente propagada para as subclasses.
         <br>
          <h4><strong> Desvantagens:</strong> </h4>
         1. Fraco encapsulamento entre classes e subclasses e o forte acoplamento entre elas onde ao mudar uma superclasse pode afetar todas as subclasses;
         <br>
         2. Quando um objeto precisa ser de uma classe diferente em momentos diferentes e não é possível com a herança.
         <br>
      </p>
   </li>
             
   <li>
      Por que a interface Serializable é necessária ao efetuar persistência em arquivos binários?
      <p> 
         Essa interface permite que os objetos sejam serializados(convertidos em uma sequência de bytes) e
desserializados com a conversão de volta à um objeto.
      </p>
   </li>
   
   <li>
      Como o paradigma funcional é utilizado pela API stream no Java?
      <p> 
         A API stream é usada para manipular coleções (Collections) de uma maneira mais eficiente, utilizando funções.Ela possibilita uma iteração sobre essas coleções de objetos e, a cada elemento, realizar alguma ação, seja ela de filtragem, mapeamento, transformação, etc.
      </p>
   </li>
   
   <li>
      Quando trabalhamos com Java, qual padrão de desenvolvimento é adotado na persistência de dados em
arquivos?
      <p> 
        Nesse projeto foram utilizadas a classe ObjectOutputStream para escrever objetos em um arquivo "[prefixo].fisica.bin e [prefixo].juridica.bin"
e a classe ObjectInputStream para ler os objetos dos mesmos arquivos.
      </p>
   </li>

   <li>
      O que são elementos estáticos e qual o motivo para o método main adotar esse 
modificador?
      <p> 
         Os elementos estáticos são elementos que "existem", ou seja, estão disponíveis para uso, sem a necessidade de serem instânciados. Podem ser utilizados em código sem a necessidade de existirem objetos produzidos (sem a necessidade de um comando "new Classe()").
      </p>
   </li>

   <li>
      Para que serve a classe Scanner ?
      <p> 
         Para leitura de dados de entrada (inteiros, boolean, string, etc) inseridos pelo ususario atraves do teclado. 
      </p>
   </li>

   <li>
      Como o uso de classes de repositório impactou na organização do código?
      <p> 
        As classes de repositórios serviram para gerenciar,centralizar e organizar as atividades de inserir, excluir, alterar, localizar, recuperar e salvar os dados de pessoa física e jurídica. 
      </p>
   </li>
</ul>



