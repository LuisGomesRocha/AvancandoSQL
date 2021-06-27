# AvancandoSQL

<h1 align="center">
    <a href="https://www.alura.com.br/formacao-oracle-mysql/">🔗 Consultas SQL: Avançando no SQL com MySQL </a>
</h1>

<p align="justify"> :robot: Somos muitos alunos e alunas aqui no Bootcamp. Em função de todas as avaliações respondidas, muitos dados são gerados. E agora temos alguns desafios para você :robot: </p>

<p align="justify"> :robot: Dado que todo(a) aluno(a) tem um email (máximo de 30 caracteres),nome (máximo de 30 caracteres) e idade (entre 1 e 100). O que você faria para representar essa estrutura no banco? :robot: </p>

 ``` 
-- -----------------------------------------------------
-- Schema zup_desafio
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `zup_desafio` DEFAULT CHARACTER SET utf8 ;
USE `zup_desafio` ;

-- -----------------------------------------------------
-- Table `zup_desafio`.`ALUNO`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `zup_desafio`.`ALUNO` (
  `id_aluno` INT NOT NULL AUTO_INCREMENT,
  `nome` VARCHAR(30) NULL,
  `email` VARCHAR(30) NULL,
  `idade` INT(3) NULL,
  PRIMARY KEY (`id_aluno`))
ENGINE = InnoDB;

 ``` 

<p align="justify"> :robot: Dentro do bootcamp temos também um conjunto de avaliações que são respondidas pelas pessoas. Toda avaliação tem um título e uma descrição do que precisa ser feito. O que você faria para representar essa estrutura no banco?  :robot: </p>



 ``` 
-- -----------------------------------------------------
-- Table `zup_desafio`.`AVALIACAO`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `zup_desafio`.`AVALIACAO` (
  `id_avaliacao` INT NOT NULL AUTO_INCREMENT,
  `titulo` VARCHAR(300) NULL,
  `descricao` VARCHAR(300) NULL,
  PRIMARY KEY (`id_avaliacao`))
ENGINE = InnoDB;

 ``` 

<p align="justify"> :robot: Cada aluno(a) responde uma ou mais avaliações que chamamos de cognitive walkthrough, ela tem que descrever os passos da solução dela para determinada situação problema. Toda resposta tem um campo aberto para que a pessoa consiga descrever a solução dela. É necessário que toda resposta seja linkada com a pessoa que a respondeu e também com a avaliação relativa àquela resposta. O que você faria para representar essa estrutura no banco de dados?  :robot: </p>


<p align="justify"> :robot: Além de responder sua avaliação, a pessoa também responde um outro formulário onde ela corrige sua resposta em função da resposta de um mentor(a). Essa correção sempre tem uma nota de 1 a 10 e está linkada com a avaliação respondida pela própria pessoa. O que você faria para representar essa estrutura no banco de dados? :robot: </p>


``` 
-- -----------------------------------------------------
-- Table `zup_desafio`.`QUESTOES`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `zup_desafio`.`QUESTOES` (
  `id_questao` INT NOT NULL AUTO_INCREMENT,
  `pergunta` VARCHAR(300) NULL,
  `resposta_especialista` VARCHAR(300) NULL,
  `AVALIACAO_id_avaliacao` INT NOT NULL,
  PRIMARY KEY (`id_questao`, `AVALIACAO_id_avaliacao`),
  INDEX `fk_QUESTOES_AVALIACAO_idx` (`AVALIACAO_id_avaliacao` ASC) VISIBLE,
  CONSTRAINT `fk_QUESTOES_AVALIACAO`
    FOREIGN KEY (`AVALIACAO_id_avaliacao`)
    REFERENCES `zup_desafio`.`AVALIACAO` (`id_avaliacao`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `zup_desafio`.`COGNITION`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `zup_desafio`.`COGNITION` (
  `ALUNO_id_aluno` INT NOT NULL,
  `QUESTOES_id_questao` INT NOT NULL,
  `QUESTOES_AVALIACAO_id_avaliacao` INT NOT NULL,
  `resposta` VARCHAR(300) NULL,
  `nota` INT(2) NULL DEFAULT -1,
  PRIMARY KEY (`ALUNO_id_aluno`, `QUESTOES_id_questao`, `QUESTOES_AVALIACAO_id_avaliacao`),
  INDEX `fk_ALUNO_has_QUESTOES_QUESTOES1_idx` (`QUESTOES_id_questao` ASC, `QUESTOES_AVALIACAO_id_avaliacao` ASC) VISIBLE,
  INDEX `fk_ALUNO_has_QUESTOES_ALUNO1_idx` (`ALUNO_id_aluno` ASC) VISIBLE,
  CONSTRAINT `fk_ALUNO_has_QUESTOES_ALUNO1`
    FOREIGN KEY (`ALUNO_id_aluno`)
    REFERENCES `zup_desafio`.`ALUNO` (`id_aluno`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_ALUNO_has_QUESTOES_QUESTOES1`
    FOREIGN KEY (`QUESTOES_id_questao` , `QUESTOES_AVALIACAO_id_avaliacao`)
    REFERENCES `zup_desafio`.`QUESTOES` (`id_questao` , `AVALIACAO_id_avaliacao`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;

``` 


<h1 align="center">
    <img src="https://media0.giphy.com/media/1U4S8219ByoGk/giphy.gif?cid=790b7611e0acfe2ac9ab00a83dea6024cf710449ecc11e72&rid=giphy.gif&ct=g" width="300" height="220" />
</h1>



### Features

- [x] Precisamos saber todo mundo que respondeu uma avaliação com um título específico?

``` 

select distinct(t3.ALUNO_id_aluno), t1.nome, t2.titulo as TIT_AVALIACAO from aluno as t1, avaliacao as t2 ,cognition as t3
			where t1.id_aluno = t3.ALUNO_id_aluno         and
                t2.id_avaliacao = t3.QUESTOES_AVALIACAO_id_avaliacao
                and
                t2.titulo like "%AUTO%";
``` 

- [x] Precisamos saber quantas respostas foram dadas por avaliação

``` 

SELECT AV.TITULO,COUNT(CN.*) FROM AVALIACAO AV INNER JOIN QUESTOES QT ON AV.ID_AVALIACAO = QT.AVALIACAO_ID_AVALIACAO 
									INNER JOIN COGNITION CN ON QT.id_questao = CN.QUESTOES_id_questao
									GROUP BY AV.TITULO;

``` 

- [x] Precisamos da nota média da autocorreção por avaliação


``` 
SELECT  AV.TITULO, COUNT(CN.`QUESTOES_AVALIACAO_id_avaliacao`)  FROM AVALIACAO AV INNER JOIN QUESTOES QT 
ON AV.ID_AVALIACAO = QT.AVALIACAO_ID_AVALIACAO 
INNER JOIN COGNITION CN 
ON QT.id_questao = CN.QUESTOES_id_questao
GROUP BY AV.TITULO;


``` 

