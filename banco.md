
### Cardinalidades

**Users e AssemblyLines**

A relação entre a tabela Users e AssemblyLines é de N para 1, já que vários funcionarios podem estar ligados a uma linha, mas um funcionário não pode estar em várias linhas.

**Users e Favorites**

Users e Favorites possuem associação 1 para 1, uma vez que um usuário terá apenas um favorito, que está atribuido ao seu id.


**Users e Tasks**

As entidades Users e Tasks possuem relacionamento 1 para N, em que uma Task de estudo dos manuais pode ser atribuída para diversos usuários ao mesmo tempo.

**AssemblyLine e Products**

As tabelas AssemblyLines e Products possuem relação 1 para N, uma vez que uma linha de montagem pode conter diversos produtos.

**Favorites e Handbooks**

A entidade Favorites se relaciona com Handbooks em associação 1 para 1, já que para cada item favorito adicionado, é associado um unico manual.

**Products e Handbooks**

Similar à relação entre Favorites e Handbooks, as tabelas Products e Handbooks também se relacionam 1 para 1, uma vez que cada produto tem um manual.

**Handbooks e Tasks**

As entidades Handbooks e Tasks possuem associação 1 para 1, uma vez que cada tarefa task é de apenas um manual.

**Handbooks e AdditionalFiles**

A tabela Handbooks se associa com AdditionalFiles em um relacionamento 1 para N, uma vez que poderão haver muitos arquivos adicionais para cada manual de produto.

**Handbooks e HandbookVersions**

Em relacionamento 1 para N, Handbooks e HandbookVersions se associam de maneira a permitir várias versões de manuais (atualizações ao longo do tempo) para cada manual específico.

### Conexões entre chaves primárias e estrangeiras

**AssemblerIds (foreign key)**: Por se tratar de uma chave estrangeira, esse atributo referência os IDs do montador da entidade users, ou seja, este atributo é utilizado para identificar o montador, e estará nesta entidade como chave estrangeira, pois é necessário que as tarefas sejam atribuídas para alguém. Desta forma, o ID representa para quem a tarefa é direcionada.

**AssociatedHandbookIds (foreign key)**: Por se tratar de uma chave estrangeira, esse atributo referência os IDs do manual da entidade handbooks, ou seja, este atributo é utilizado para identificar o manual, e estará nesta entidade como chave estrangeira, pois é necessário identificar quais manuais o montador deve estudar.

**AssociatedAdminIds (foreign key)**: Por se tratar de uma chave estrangeira, esse atributo referencia os IDs dos administradores da entidade users que estão atribuindo as tarefas em questão. O tipo deste atributo é inteiro.

**UserIds (foreign key)**: Por se tratar de uma chave estrangeira, esse atributo referencia os IDs dos usuários da entidade users. Ele fica responsável por identificar qual usuário é responsável por um conjunto de manuais favoritados pelo próprio.

**HandbookIds (foreign key)**: Por se tratar de uma chave estrangeira, esse atributo referencia os IDs dos manuais da entidade Handbooks  que foram favoritados pelo usuário, a fim de identificar qual o manual escolhido pelo usuário.

**AssociatedAssemblyLineID (foreign key)**: Por se tratar de uma chave estrangeira, este atributo refere-se às linhas de montagem que foram alocadas para montar determinado produto, que serão definidas a partir dos IDs e nomes apresentados nos dois atributos acima.

**AssociatedProductIds (foreign key)**: Por se tratar de uma chave estrangeira, esse atributo referencia os IDs dos produtos associados aos manuais, presente na tabela products .

AdditionalFilesIds (foreign key): Por se tratar de uma chave estrangeira, esse atributo referencia os IDs dos manuais que podem ser adicionados pelos administradores, da tabela AdditionalFiles. Quando o administrador adiciona um manual, o ID atribuído a ele estará presente neste atributo.

AssociatedHandbookIds (foreign key):Por se tratar de uma chave estrangeira, esse atributo referencia os IDs dos manuais que estão disponíveis dentro do banco de dados, presentes na tabela Handbooks.

AssociatedAdministratorId (foreign key): Por se tratar de uma chave estrangeira, este atributo refere-se ao ID do engenheiro encarregado pela linha de montagem em questão, presente na tabela users.

<div align="center">
<sub>Banco de dados logico</sub>
<img src="Captura de tela 2024-05-10 232046.png" width="100%" >
<sup>Fonte: Material produzido pela Dell Design System (2018)</sup>
</div>

A partir deste modelo lógico, criou-se os seguintes códigos a fim de criar o modelo fisico do DBeaver:
### Codigo em .sqk
```javascript
CREATE TABLE IF NOT EXISTS "users" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"Names" text NOT NULL,
	"Emails" text NOT NULL,
	"Passwords" text NOT NULL,
	PRIMARY KEY ("Ids")
);

CREATE TABLE IF NOT EXISTS "Handbooks" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"AssociatedProductIds" bigint NOT NULL,
	"Names" text NOT NULL,
	"Descriptions" text NOT NULL,
	"UploadDates" timestamp with time zone NOT NULL,
	"AdditionalFilesIds" bigint NOT NULL,
	"PublicationDates" timestamp with time zone NOT NULL,
	PRIMARY KEY ("Ids")
);

CREATE TABLE IF NOT EXISTS "Tasks" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"AssemblerIds" bigint NOT NULL,
	"AssociatedHandbookIds" bigint NOT NULL,
	"IsFinished" boolean NOT NULL,
	"UploadDates" timestamp with time zone NOT NULL,
	"Priorities" text NOT NULL,
	"AssociatedAdminIds" bigint NOT NULL,
	PRIMARY KEY ("Ids")
);

CREATE TABLE IF NOT EXISTS "Favorites" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"UserIds" bigint NOT NULL,
	"HandbookIds" bigint NOT NULL,
	"HandbookDescriptions" text NOT NULL,
	"HandbookNames" text NOT NULL,
	PRIMARY KEY ("Ids")
);

CREATE TABLE IF NOT EXISTS "AssembleLine" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"Names" text NOT NULL,
	"Descriptions" text NOT NULL,
	"AssociatedAdministratorId" bigint NOT NULL,
	PRIMARY KEY ("Ids")
);

CREATE TABLE IF NOT EXISTS "Products" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"Names" text NOT NULL,
	"AssociatedAssembleLineId" bigint NOT NULL,
	PRIMARY KEY ("Ids")
);

CREATE TABLE IF NOT EXISTS "AdditionalFiles" (
	"Ids" bigint GENERATED ALWAYS AS IDENTITY NOT NULL UNIQUE,
	"AssociatedPublicationDates" timestamp with time zone NOT NULL,
	"TypesOfFiles" text NOT NULL,
	"FilesPath" text NOT NULL,
	PRIMARY KEY ("Ids")
);


ALTER TABLE "Handbooks" ADD CONSTRAINT "Handbooks_fk1" FOREIGN KEY ("AssociatedProductIds") REFERENCES "Products"("Ids");

ALTER TABLE "Handbooks" ADD CONSTRAINT "Handbooks_fk5" FOREIGN KEY ("AdditionalFilesIds") REFERENCES "AdditionalFiles"("Ids");
ALTER TABLE "Tasks" ADD CONSTRAINT "Tasks_fk1" FOREIGN KEY ("AssemblerIds") REFERENCES "users"("Ids");

ALTER TABLE "Tasks" ADD CONSTRAINT "Tasks_fk2" FOREIGN KEY ("AssociatedHandbookIds") REFERENCES "Handbooks"("Ids");

ALTER TABLE "Tasks" ADD CONSTRAINT "Tasks_fk6" FOREIGN KEY ("AssociatedAdminIds") REFERENCES "users"("Ids");
ALTER TABLE "Favorites" ADD CONSTRAINT "Favorites_fk1" FOREIGN KEY ("UserIds") REFERENCES "users"("Ids");

ALTER TABLE "Favorites" ADD CONSTRAINT "Favorites_fk2" FOREIGN KEY ("HandbookIds") REFERENCES "Handbooks"("Ids");
ALTER TABLE "AssembleLine" ADD CONSTRAINT "AssembleLine_fk3" FOREIGN KEY ("AssociatedAdministratorId") REFERENCES "users"("Ids");
ALTER TABLE "Products" ADD CONSTRAINT "Products_fk2" FOREIGN KEY ("AssociatedAssembleLineId") REFERENCES "AssembleLine"("Ids");

```
### Codigo em .xml

```javascript
<?xml version="1.0" encoding="UTF-8"?>
<database>
    <table name="users">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="Names" type="text" not-null="true"/>
        <column name="Emails" type="text" not-null="true"/>
        <column name="Passwords" type="text" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
    </table>

    <table name="Handbooks">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="AssociatedProductIds" type="bigint" not-null="true"/>
        <column name="Names" type="text" not-null="true"/>
        <column name="Descriptions" type="text" not-null="true"/>
        <column name="UploadDates" type="timestamp with time zone" not-null="true"/>
        <column name="AdditionalFilesIds" type="bigint" not-null="true"/>
        <column name="PublicationDates" type="timestamp with time zone" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
        <foreign-key name="Handbooks_fk1" foreignTable="Products">
            <column>AssociatedProductIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
        <foreign-key name="Handbooks_fk5" foreignTable="AdditionalFiles">
            <column>AdditionalFilesIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
    </table>

    <table name="Tasks">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="AssemblerIds" type="bigint" not-null="true"/>
        <column name="AssociatedHandbookIds" type="bigint" not-null="true"/>
        <column name="IsFinished" type="boolean" not-null="true"/>
        <column name="UploadDates" type="timestamp with time zone" not-null="true"/>
        <column name="Priorities" type="text" not-null="true"/>
        <column name="AssociatedAdminIds" type="bigint" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
        <foreign-key name="Tasks_fk1" foreignTable="users">
            <column>AssemblerIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
        <foreign-key name="Tasks_fk2" foreignTable="Handbooks">
            <column>AssociatedHandbookIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
        <foreign-key name="Tasks_fk6" foreignTable="users">
            <column>AssociatedAdminIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
    </table>

    <table name="Favorites">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="UserIds" type="bigint" not-null="true"/>
        <column name="HandbookIds" type="bigint" not-null="true"/>
        <column name="HandbookDescriptions" type="text" not-null="true"/>
        <column name="HandbookNames" type="text" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
        <foreign-key name="Favorites_fk1" foreignTable="users">
            <column>UserIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
        <foreign-key name="Favorites_fk2" foreignTable="Handbooks">
            <column>HandbookIds</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
    </table>

    <table name="AssembleLine">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="Names" type="text" not-null="true"/>
        <column name="Descriptions" type="text" not-null="true"/>
        <column name="AssociatedAdministratorId" type="bigint" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
        <foreign-key name="AssembleLine_fk3" foreignTable="users">
            <column>AssociatedAdministratorId</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
    </table>

    <table name="Products">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="Names" type="text" not-null="true"/>
        <column name="AssociatedAssembleLineId" type="bigint" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
        <foreign-key name="Products_fk2" foreignTable="AssembleLine">
            <column>AssociatedAssembleLineId</column>
            <referenceColumn>Ids</referenceColumn>
        </foreign-key>
    </table>

    <table name="AdditionalFiles">
        <column name="Ids" type="bigint" generated="ALWAYS" not-null="true" unique="true"/>
        <column name="AssociatedPublicationDates" type="timestamp with time zone" not-null="true"/>
        <column name="TypesOfFiles" type="text" not-null="true"/>
        <column name="FilesPath" type="text" not-null="true"/>
        <primary-key>
            <column>Ids</column>
        </primary-key>
    </table>
</database>

```

