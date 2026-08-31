CREATE DATABASE  IF NOT EXISTS `tukotomi` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */ /*!80016 DEFAULT ENCRYPTION='N' */;
USE `tukotomi`;
-- MySQL dump 10.13  Distrib 8.0.46, for Win64 (x86_64)
--
-- Host: localhost    Database: tukotomi
-- ------------------------------------------------------
-- Server version	8.0.46

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `agendamento`
--

DROP TABLE IF EXISTS `agendamento`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `agendamento` (
  `id_agendamento` int NOT NULL AUTO_INCREMENT,
  `data` date NOT NULL,
  `hora_inicio` time NOT NULL,
  `hora_fim` time NOT NULL,
  `status` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `ordem_pedido` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `fk_cliente` int NOT NULL,
  `fk_profissional` int NOT NULL,
  `fk_cliente_pacote` int DEFAULT NULL,
  `nome_cliente_avulso` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `telefone_cliente_avulso` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `valor_total` double NOT NULL,
  `fk_servico` int DEFAULT NULL,
  PRIMARY KEY (`id_agendamento`),
  KEY `fk_ag_cliente` (`fk_cliente`),
  KEY `fk_ag_profissional` (`fk_profissional`),
  KEY `fk_ag_cliente_pacote` (`fk_cliente_pacote`),
  KEY `FK3mwegbewvx1xlnnfi4navbssc` (`fk_servico`),
  CONSTRAINT `FK3mwegbewvx1xlnnfi4navbssc` FOREIGN KEY (`fk_servico`) REFERENCES `servico` (`id_servico`),
  CONSTRAINT `fk_ag_cliente` FOREIGN KEY (`fk_cliente`) REFERENCES `cliente` (`id_cliente`) ON DELETE CASCADE,
  CONSTRAINT `fk_ag_cliente_pacote` FOREIGN KEY (`fk_cliente_pacote`) REFERENCES `cliente_pacote` (`id_cliente_pacote`) ON DELETE SET NULL,
  CONSTRAINT `fk_ag_profissional` FOREIGN KEY (`fk_profissional`) REFERENCES `profissional` (`id_profissional`) ON DELETE CASCADE,
  CONSTRAINT `chk_horario_valido` CHECK ((`hora_fim` > `hora_inicio`))
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `agendamento_servico`
--

DROP TABLE IF EXISTS `agendamento_servico`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `agendamento_servico` (
  `id_agendamento_servico` int NOT NULL AUTO_INCREMENT,
  `fk_agendamento` int NOT NULL,
  `fk_servico` int NOT NULL,
  `fk_cliente_pacote_servico` int DEFAULT NULL,
  PRIMARY KEY (`id_agendamento_servico`),
  UNIQUE KEY `unique_agendamento_servico` (`fk_agendamento`,`fk_servico`),
  KEY `fk_as_servico` (`fk_servico`),
  KEY `fk_as_cliente_pacote_servico` (`fk_cliente_pacote_servico`),
  CONSTRAINT `fk_as_agendamento` FOREIGN KEY (`fk_agendamento`) REFERENCES `agendamento` (`id_agendamento`) ON DELETE CASCADE,
  CONSTRAINT `fk_as_cliente_pacote_servico` FOREIGN KEY (`fk_cliente_pacote_servico`) REFERENCES `cliente_pacote_servico` (`id_cliente_pacote_servico`) ON DELETE SET NULL,
  CONSTRAINT `fk_as_servico` FOREIGN KEY (`fk_servico`) REFERENCES `servico` (`id_servico`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `cliente`
--

DROP TABLE IF EXISTS `cliente`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `cliente` (
  `id_cliente` int NOT NULL AUTO_INCREMENT,
  `observacoes` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `fk_usuario` int DEFAULT NULL,
  PRIMARY KEY (`id_cliente`),
  UNIQUE KEY `fk_usuario` (`fk_usuario`),
  CONSTRAINT `fk_cliente_usuario` FOREIGN KEY (`fk_usuario`) REFERENCES `usuario` (`id_usuario`) ON DELETE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `cliente_pacote`
--

DROP TABLE IF EXISTS `cliente_pacote`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `cliente_pacote` (
  `id_cliente_pacote` int NOT NULL AUTO_INCREMENT,
  `fk_cliente` int NOT NULL,
  `fk_pacote` int NOT NULL,
  `status` enum('ATIVO','INATIVO') COLLATE utf8mb4_unicode_ci DEFAULT 'ATIVO',
  `data_compra` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `expiracao` timestamp NULL DEFAULT NULL,
  `ativo` bit(1) NOT NULL,
  `dt_expiracao` datetime(6) NOT NULL,
  PRIMARY KEY (`id_cliente_pacote`),
  KEY `fk_cp_cliente` (`fk_cliente`),
  KEY `fk_cp_pacote` (`fk_pacote`),
  CONSTRAINT `fk_cp_cliente` FOREIGN KEY (`fk_cliente`) REFERENCES `cliente` (`id_cliente`) ON DELETE CASCADE,
  CONSTRAINT `fk_cp_pacote` FOREIGN KEY (`fk_pacote`) REFERENCES `pacote` (`id_pacote`) ON DELETE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `cliente_pacote_servico`
--

DROP TABLE IF EXISTS `cliente_pacote_servico`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `cliente_pacote_servico` (
  `id_cliente_pacote_servico` int NOT NULL AUTO_INCREMENT,
  `fk_cliente_pacote` int NOT NULL,
  `fk_servico` int NOT NULL,
  `quantidade_disponivel` int NOT NULL,
  PRIMARY KEY (`id_cliente_pacote_servico`),
  UNIQUE KEY `unique_cliente_pacote_servico` (`fk_cliente_pacote`,`fk_servico`),
  KEY `fk_cps_servico` (`fk_servico`),
  CONSTRAINT `fk_cps_cliente_pacote` FOREIGN KEY (`fk_cliente_pacote`) REFERENCES `cliente_pacote` (`id_cliente_pacote`) ON DELETE CASCADE,
  CONSTRAINT `fk_cps_servico` FOREIGN KEY (`fk_servico`) REFERENCES `servico` (`id_servico`) ON DELETE CASCADE,
  CONSTRAINT `cliente_pacote_servico_chk_1` CHECK ((`quantidade_disponivel` >= 0))
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `comprovante`
--

DROP TABLE IF EXISTS `comprovante`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `comprovante` (
  `id_comprovante` int NOT NULL AUTO_INCREMENT,
  `url` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `fk_pagamento` int DEFAULT NULL,
  PRIMARY KEY (`id_comprovante`),
  UNIQUE KEY `fk_pagamento` (`fk_pagamento`),
  CONSTRAINT `fk_comprovante_pagamento` FOREIGN KEY (`fk_pagamento`) REFERENCES `pagamento` (`id_pagamento`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `pacote`
--

DROP TABLE IF EXISTS `pacote`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `pacote` (
  `id_pacote` int NOT NULL AUTO_INCREMENT,
  `nome` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `descricao` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `preco_total` double NOT NULL,
  PRIMARY KEY (`id_pacote`),
  CONSTRAINT `pacote_chk_1` CHECK ((`preco_total` >= 0))
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `pacote_servico`
--

DROP TABLE IF EXISTS `pacote_servico`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `pacote_servico` (
  `id_pacote_servico` int NOT NULL AUTO_INCREMENT,
  `fk_pacote` int NOT NULL,
  `fk_servico` int NOT NULL,
  PRIMARY KEY (`id_pacote_servico`),
  UNIQUE KEY `unique_pacote_servico` (`fk_pacote`,`fk_servico`),
  KEY `fk_ps_servico` (`fk_servico`),
  CONSTRAINT `fk_ps_pacote` FOREIGN KEY (`fk_pacote`) REFERENCES `pacote` (`id_pacote`) ON DELETE CASCADE,
  CONSTRAINT `fk_ps_servico` FOREIGN KEY (`fk_servico`) REFERENCES `servico` (`id_servico`) ON DELETE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `pagamento`
--

DROP TABLE IF EXISTS `pagamento`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `pagamento` (
  `id_pagamento` int NOT NULL AUTO_INCREMENT,
  `valor` double NOT NULL,
  `metodo` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `status` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `data` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `fk_agendamento` int DEFAULT NULL,
  PRIMARY KEY (`id_pagamento`),
  KEY `fk_pagamento_agendamento` (`fk_agendamento`),
  CONSTRAINT `fk_pagamento_agendamento` FOREIGN KEY (`fk_agendamento`) REFERENCES `agendamento` (`id_agendamento`) ON DELETE SET NULL,
  CONSTRAINT `pagamento_chk_1` CHECK ((`valor` >= 0))
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `produto`
--

DROP TABLE IF EXISTS `produto`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `produto` (
  `id_produto` int NOT NULL AUTO_INCREMENT,
  `nome` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL,
  `unidade_medida` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `custo_unitario` double NOT NULL,
  PRIMARY KEY (`id_produto`),
  CONSTRAINT `produto_chk_1` CHECK ((`custo_unitario` >= 0))
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `profissional`
--

DROP TABLE IF EXISTS `profissional`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `profissional` (
  `id_profissional` int NOT NULL AUTO_INCREMENT,
  `especialidade` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `descricao` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `foto` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `fk_usuario` int DEFAULT NULL,
  PRIMARY KEY (`id_profissional`),
  UNIQUE KEY `fk_usuario` (`fk_usuario`),
  CONSTRAINT `fk_profissional_usuario` FOREIGN KEY (`fk_usuario`) REFERENCES `usuario` (`id_usuario`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `profissional_servico`
--

DROP TABLE IF EXISTS `profissional_servico`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `profissional_servico` (
  `profissional_id` int NOT NULL,
  `servico_id` int NOT NULL,
  KEY `FKouca0bkihu6b472631llper5o` (`servico_id`),
  KEY `FK2aj8wirvp0eb2ctmr3pab2e42` (`profissional_id`),
  CONSTRAINT `FK2aj8wirvp0eb2ctmr3pab2e42` FOREIGN KEY (`profissional_id`) REFERENCES `profissional` (`id_profissional`),
  CONSTRAINT `FKouca0bkihu6b472631llper5o` FOREIGN KEY (`servico_id`) REFERENCES `servico` (`id_servico`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `servico`
--

DROP TABLE IF EXISTS `servico`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `servico` (
  `id_servico` int NOT NULL AUTO_INCREMENT,
  `nome` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `duracao_minutos` int NOT NULL,
  `descricao` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `preco` double NOT NULL,
  `status` enum('ATIVO','INATIVO') COLLATE utf8mb4_unicode_ci DEFAULT 'ATIVO',
  `ativo` bit(1) NOT NULL,
  PRIMARY KEY (`id_servico`),
  CONSTRAINT `servico_chk_1` CHECK ((`duracao_minutos` > 0)),
  CONSTRAINT `servico_chk_2` CHECK ((`preco` >= 0))
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `servico_produto`
--

DROP TABLE IF EXISTS `servico_produto`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `servico_produto` (
  `id_servico_produto` int NOT NULL AUTO_INCREMENT,
  `fk_servico` int NOT NULL,
  `fk_produto` int NOT NULL,
  `quantidade_usada` double NOT NULL,
  PRIMARY KEY (`id_servico_produto`),
  UNIQUE KEY `unique_servico_produto` (`fk_servico`,`fk_produto`),
  KEY `fk_sprod_produto` (`fk_produto`),
  CONSTRAINT `fk_sprod_produto` FOREIGN KEY (`fk_produto`) REFERENCES `produto` (`id_produto`) ON DELETE CASCADE,
  CONSTRAINT `fk_sprod_servico` FOREIGN KEY (`fk_servico`) REFERENCES `servico` (`id_servico`) ON DELETE CASCADE,
  CONSTRAINT `servico_produto_chk_1` CHECK ((`quantidade_usada` > 0))
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `servico_profissional`
--

DROP TABLE IF EXISTS `servico_profissional`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `servico_profissional` (
  `id_profissional_servico` int NOT NULL AUTO_INCREMENT,
  `fk_servico` int NOT NULL,
  `fk_profissional` int NOT NULL,
  PRIMARY KEY (`id_profissional_servico`),
  UNIQUE KEY `unique_servico_profissional` (`fk_servico`,`fk_profissional`),
  KEY `fk_sp_profissional` (`fk_profissional`),
  CONSTRAINT `fk_sp_profissional` FOREIGN KEY (`fk_profissional`) REFERENCES `profissional` (`id_profissional`) ON DELETE CASCADE,
  CONSTRAINT `fk_sp_servico` FOREIGN KEY (`fk_servico`) REFERENCES `servico` (`id_servico`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Table structure for table `usuario`
--

DROP TABLE IF EXISTS `usuario`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!50503 SET character_set_client = utf8mb4 */;
CREATE TABLE `usuario` (
  `id_usuario` int NOT NULL AUTO_INCREMENT,
  `nome` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `telefone` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `cpf` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `senha` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `email` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `tipo` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `status` enum('ATIVO','INATIVO') COLLATE utf8mb4_unicode_ci DEFAULT 'ATIVO',
  `criacao` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  `ativo` bit(1) NOT NULL,
  PRIMARY KEY (`id_usuario`),
  UNIQUE KEY `cpf` (`cpf`),
  UNIQUE KEY `email` (`email`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
/*!40101 SET character_set_client = @saved_cs_client */;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2026-08-31 13:57:41
