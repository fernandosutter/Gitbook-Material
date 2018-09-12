# EXERCÍCIOS Procedure

1. Neste exercício vamos criar um banco de dados para armazenar os dados dos alunos de uma universidade. Além de desenhar o diagrama, criar o banco de dados e seus objetos, você deverá criar os scripts de população básica. Em seguida deverá criar as procedures que irão executar as operações de manipulação das notas e faltas. Abaixo uma sugestão de parte da solução:

```text
    USE MASTER
    ALTER DATABASE Universidade SET SINGLE_USER WITH ROLLBACK IMMEDIATE
    GO
    DROP DATABASE Universidade;
    GO
    USE master;
    CREATE DATABASE Universidade;
    GO
    USE Universidade;
    GO
    CREATE TABLE ALUNOS (MATRICULA INT NOT NULL IDENTITY CONSTRAINT PK_ALUNO PRIMARY KEY, NOME VARCHAR(50) NOT NULL);
    GO
    CREATE TABLE CURSOS (CURSO CHAR(3) NOT NULL CONSTRAINT PK_CURSO PRIMARY KEY, NOME VARCHAR(50) NOT NULL);
    GO
    CREATE TABLE PROFESSOR (PROFESSOR INT IDENTITY NOT NULL CONSTRAINT PK_PROFESSOR PRIMARY KEY, NOME VARCHAR(50) NOT NULL);
    GO
    CREATE TABLE MATERIAS (SIGLA CHAR(3) NOT NULL, NOME VARCHAR(50) NOT NULL, CARGAHORARIA INT NOT NULL,
    CURSO CHAR(3) NOT NULL, PROFESSOR INT
    CONSTRAINT PK_MATERIA PRIMARY KEY (SIGLA,CURSO,PROFESSOR)
    CONSTRAINT FK_CURSO FOREIGN KEY (CURSO) REFERENCES CURSOS(CURSO),
    CONSTRAINT FK_PROFESSOR FOREIGN KEY (PROFESSOR) REFERENCES PROFESSOR (PROFESSOR)
    );
    GO
    INSERT ALUNOS (NOME) VALUES ('Pedro')
    GO
    INSERT CURSOS (CURSO, NOME) VALUES ('SIS','SISTEMAS'),('ENG','ENGENHARIA')
    GO
    INSERT PROFESSOR (NOME ) VALUES ('DORNEL'),('WALTER')
    GO
    INSERT MATERIAS (SIGLA, NOME, CARGAHORARIA, CURSO,PROFESSOR)
    VALUES ('BDA','BANCO DE DADOS',144,'SIS',1), ('PRG','PROGRAMAÇÃO',144,'SIS',2)
    GO
    INSERT MATERIAS (SIGLA, NOME, CARGAHORARIA, CURSO,PROFESSOR)
    VALUES ('BDA','BANCO DE DADOS',144,'ENG',1), ('PRG','PROGRAMAÇÃO',144,'ENG',2)
    GO
    CREATE TABLE MATRICULA (MATRICULA INT, CURSO CHAR(3), MATERIA CHAR(3), PROFESSOR INT, PERLETIVO INT,
    N1 FLOAT, N2 FLOAT, N3 FLOAT, N4 FLOAT, TOTALPONTOS FLOAT, MEDIA FLOAT,
    F1 INT, F2 INT, F3 INT, F4 INT, TOTALFALTAS INT, PERCFREQ FLOAT, RESULTADO VARCHAR(20)

    CONSTRAINT PK_MATRICULA PRIMARY KEY (MATRICULA,CURSO,MATERIA,PROFESSOR,PERLETIVO),
    CONSTRAINT FK_ALUNOS_MATRICULA FOREIGN KEY (MATRICULA) REFERENCES ALUNOS (MATRICULA),
    CONSTRAINT FK_CURSOS_MATRICULA FOREIGN KEY (CURSO) REFERENCES CURSOS (CURSO),
    --CONSTRAINT FK_MATERIAS FOREIGN KEY (MATERIA) REFERENCES MATERIAS (SIGLA),
    CONSTRAINT FK_PROFESSOR_MATRICULA FOREIGN KEY (PROFESSOR) REFERENCES PROFESSOR(PROFESSOR)
    )
    -->PROC MATRICULA
    --CREATE PROCEDURE procMATRICULAALUNO
    --(
    --@NOME VARCHAR(50),
    --@CURSO CHAR(3)
    --)
    --AS
    --BEGIN
    --SELECT * FROM ALUNOS WHERE NOME = @NOME

    --SELECT * FROM MATERIAS WHERE CURSO = @CURSO
    --END
    --GO
    --EXEC procMATRICULAALUNO @NOME = 'Pedro', -- varchar(50)
    --                        @CURSO = 'SIS' -- char(3)

    --Calculo do percentual de Frequencia (NrFaltas-144*100)/144
```

 Exemplo de INSERT com SELECT

```text
    INSERT MATRICULA
    (
            MATRICULA,
            CURSO,
            MATERIA,
            PROFESSOR,
            PERLETIVO

    )
    SELECT 1 AS MATRICULA, CURSO, SIGLA,PROFESSOR, YEAR(GETDATE()) FROM MATERIAS WHERE CURSO ='ENG'
```

 Exemplo de PROCEDURE para inserir \(atualizar\) as notas

```text
    CREATE PROCEDURE sp_CadastraNotas
    (
    @MATRICULA INT, @CURSO CHAR(3), @MATERIA CHAR(3)
    --,@PROFESSOR INT
    ,@PERLETIVO CHAR(4)
    ,@NOTA FLOAT
    ,@FALTA INT
    --,@PARAMETRO
    )
    AS
    BEGIN

    UPDATE MATRICULA SET N1 = @NOTA, F1=@FALTA
    WHERE MATRICULA = @MATRICULA AND CURSO = @CURSO
    AND MATERIA = @MATERIA AND PERLETIVO = @PERLETIVO

    END
```

 Exemplo de execução da PROCEDURE para inserir \(atualizar\) as notas

```text
    EXEC sp_CadastraNotas @MATRICULA = 1,  -- int
                                              @CURSO = 'ENG',     -- char(3)
                                              @MATERIA = 'BDA',   -- char(3)
                                              @PERLETIVO = '2018', -- char(4)
                                              @NOTA = 7.0,     -- float
                                              @FALTA = 4       -- int
```


