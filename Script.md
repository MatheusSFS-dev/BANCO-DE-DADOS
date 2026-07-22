-- =========================
-- ENUMS
-- =========================
CREATE TYPE status_ativo_enum AS ENUM (
    'ATIVO',
    'INATIVO'
);

CREATE TYPE status_agendamento_enum AS ENUM (
    'PENDENTE',
    'CONFIRMADO',
    'CANCELADO',
    'CONCLUIDO'
);

CREATE TYPE status_pagamento_enum AS ENUM (
    'PENDENTE',
    'PAGO',
    'ESTORNADO',
    'CANCELADO'
);

CREATE TYPE metodo_pagamento_enum AS ENUM (
    'PIX',
    'CARTAO',
    'DINHEIRO',
    'BOLETO'
);

-- =========================
-- USUARIO
-- =========================
CREATE TABLE usuario (
    id_usuario UUID PRIMARY KEY,
    nome VARCHAR(115) NOT NULL,
    telefone VARCHAR(20),
    cpf VARCHAR(12) UNIQUE NOT NULL,
    senha VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    tipo VARCHAR(45),
    status status_ativo_enum DEFAULT 'ATIVO',
    criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- =========================
-- CLIENTE
-- =========================
CREATE TABLE cliente (
    id_cliente UUID PRIMARY KEY,
    observacoes TEXT,
    fk_usuario UUID UNIQUE,
    CONSTRAINT fk_cliente_usuario
        FOREIGN KEY (fk_usuario)
        REFERENCES usuario(id_usuario)
        ON DELETE CASCADE
);

-- =========================
-- PROFISSIONAL
-- =========================
CREATE TABLE profissional (
    id_profissional UUID PRIMARY KEY,
    especialidade VARCHAR(45),
    descricao VARCHAR(255),
    foto VARCHAR(225),
    fk_usuario UUID UNIQUE,
    CONSTRAINT fk_profissional_usuario
        FOREIGN KEY (fk_usuario)
        REFERENCES usuario(id_usuario)
        ON DELETE CASCADE
);

-- =========================
-- SERVICO
-- =========================
CREATE TABLE servico (
    id_servico UUID PRIMARY KEY,
    nome VARCHAR(45) NOT NULL,
    duracao_minutos INT NOT NULL CHECK (duracao_minutos > 0),
    descricao VARCHAR(255),
    preco NUMERIC(10,2) NOT NULL CHECK (preco >= 0),
    status status_ativo_enum DEFAULT 'ATIVO'
);

-- =========================
-- PACOTE
-- =========================
CREATE TABLE pacote (
    id_pacote UUID PRIMARY KEY,
    nome VARCHAR(45) NOT NULL,
    descricao VARCHAR(255),
    preco_total NUMERIC(10,2) CHECK (preco_total >= 0)
);

-- =========================
-- PACOTE_SERVICO
-- =========================
CREATE TABLE pacote_servico (
    id_pacote_servico UUID PRIMARY KEY,
    fk_pacote UUID NOT NULL,
    fk_servico UUID NOT NULL,
    quantidade INT NOT NULL CHECK (quantidade > 0),
    CONSTRAINT fk_ps_pacote
        FOREIGN KEY (fk_pacote)
        REFERENCES pacote(id_pacote)
        ON DELETE CASCADE,
    CONSTRAINT fk_ps_servico
        FOREIGN KEY (fk_servico)
        REFERENCES servico(id_servico)
        ON DELETE CASCADE,
    CONSTRAINT unique_pacote_servico
        UNIQUE (fk_pacote, fk_servico)
);

-- =========================
-- CLIENTE_PACOTE
-- =========================
CREATE TABLE cliente_pacote (
    id_cliente_pacote UUID PRIMARY KEY,
    fk_cliente UUID NOT NULL,
    fk_pacote UUID NOT NULL,
    status status_ativo_enum DEFAULT 'ATIVO',
    data_compra TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expiracao TIMESTAMP,
    CONSTRAINT fk_cp_cliente
        FOREIGN KEY (fk_cliente)
        REFERENCES cliente(id_cliente)
        ON DELETE CASCADE,
    CONSTRAINT fk_cp_pacote
        FOREIGN KEY (fk_pacote)
        REFERENCES pacote(id_pacote)
        ON DELETE CASCADE
);

-- =========================
-- CLIENTE_PACOTE_SERVICO
-- =========================
CREATE TABLE cliente_pacote_servico (
    id_cliente_pacote_servico UUID PRIMARY KEY,
    fk_cliente_pacote UUID NOT NULL,
    fk_servico UUID NOT NULL,
    quantidade_disponivel INT NOT NULL
        CHECK (quantidade_disponivel >= 0),
    CONSTRAINT fk_cps_cliente_pacote
        FOREIGN KEY (fk_cliente_pacote)
        REFERENCES cliente_pacote(id_cliente_pacote)
        ON DELETE CASCADE,
    CONSTRAINT fk_cps_servico
        FOREIGN KEY (fk_servico)
        REFERENCES servico(id_servico)
        ON DELETE CASCADE,
    CONSTRAINT unique_cliente_pacote_servico
        UNIQUE (fk_cliente_pacote, fk_servico)
);

-- =========================
-- SERVICO_PROFISSIONAL
-- =========================
CREATE TABLE servico_profissional (
    id_profissional_servico UUID PRIMARY KEY,
    fk_servico UUID NOT NULL,
    fk_profissional UUID NOT NULL,
    CONSTRAINT fk_sp_servico
        FOREIGN KEY (fk_servico)
        REFERENCES servico(id_servico)
        ON DELETE CASCADE,
    CONSTRAINT fk_sp_profissional
        FOREIGN KEY (fk_profissional)
        REFERENCES profissional(id_profissional)
        ON DELETE CASCADE,
    CONSTRAINT unique_servico_profissional UNIQUE (fk_servico, fk_profissional)
);

-- =========================
-- AGENDAMENTO
-- =========================
CREATE TABLE agendamento (
    id_agendamento UUID PRIMARY KEY,
    data DATE NOT NULL,
    hora_inicio TIME NOT NULL,
    hora_fim TIME NOT NULL,
    status status_agendamento_enum NOT NULL,
    ordem_pedido VARCHAR(255),
    fk_cliente UUID NOT NULL,
    fk_profissional UUID NOT NULL,
    fk_cliente_pacote UUID,
    CONSTRAINT fk_ag_cliente
        FOREIGN KEY (fk_cliente)
        REFERENCES cliente(id_cliente)
        ON DELETE CASCADE,
    CONSTRAINT fk_ag_profissional
        FOREIGN KEY (fk_profissional)
        REFERENCES profissional(id_profissional)
        ON DELETE CASCADE,
    CONSTRAINT chk_horario_valido CHECK (hora_fim > hora_inicio),
    CONSTRAINT fk_ag_cliente_pacote
    FOREIGN KEY (fk_cliente_pacote)
    REFERENCES cliente_pacote(id_cliente_pacote)
    ON DELETE SET NULL
);

-- =========================
-- AGENDAMENTO_SERVICO
-- =========================
CREATE TABLE agendamento_servico (
    id_agendamento_servico UUID PRIMARY KEY,
    fk_agendamento UUID NOT NULL,
    fk_servico UUID NOT NULL,
    fk_cliente_pacote_servico UUID,
    CONSTRAINT fk_as_agendamento
        FOREIGN KEY (fk_agendamento)
        REFERENCES agendamento(id_agendamento)
        ON DELETE CASCADE,
    CONSTRAINT fk_as_servico
        FOREIGN KEY (fk_servico)
        REFERENCES servico(id_servico)
        ON DELETE CASCADE,
    CONSTRAINT unique_agendamento_servico UNIQUE (fk_agendamento, fk_servico),
    CONSTRAINT fk_as_cliente_pacote_servico
      FOREIGN KEY (fk_cliente_pacote_servico)
      REFERENCES cliente_pacote_servico(id_cliente_pacote_servico)
      ON DELETE SET NULL
);

-- =========================
-- PAGAMENTO
-- =========================
CREATE TABLE pagamento (
    id_pagamento UUID PRIMARY KEY,
    valor NUMERIC(10,2) NOT NULL CHECK (valor >= 0),
    metodo metodo_pagamento_enum,
    status status_pagamento_enum,
    data TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    fk_agendamento UUID,
    CONSTRAINT fk_pagamento_agendamento
        FOREIGN KEY (fk_agendamento)
        REFERENCES agendamento(id_agendamento)
        ON DELETE SET NULL
);

-- =========================
-- COMPROVANTE
-- =========================
CREATE TABLE comprovante (
    id_comprovante UUID PRIMARY KEY,
    url TEXT NOT NULL,
    fk_pagamento UUID UNIQUE,
    CONSTRAINT fk_comprovante_pagamento
        FOREIGN KEY (fk_pagamento)
        REFERENCES pagamento(id_pagamento)
        ON DELETE CASCADE
);

-- =========================
-- PRODUTO
-- =========================
CREATE TABLE produto (
    id_produto UUID PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    unidade_medida VARCHAR(20), -- Ex: 'ml', 'gramas', 'unidade'
    custo_unitario NUMERIC(10,2) NOT NULL CHECK (custo_unitario >= 0)
);

-- =========================
-- SERVICO_PRODUTO
-- =========================
CREATE TABLE servico_produto (
    id_servico_produto UUID PRIMARY KEY,
    fk_servico UUID NOT NULL,
    fk_produto UUID NOT NULL,
    quantidade_usada NUMERIC(10,2) NOT NULL CHECK (quantidade_usada > 0),
    CONSTRAINT fk_sprod_servico FOREIGN KEY (fk_servico) REFERENCES servico(id_servico) ON DELETE CASCADE,
    CONSTRAINT fk_sprod_produto FOREIGN KEY (fk_produto) REFERENCES produto(id_produto) ON DELETE CASCADE,
    CONSTRAINT unique_servico_produto UNIQUE (fk_servico, fk_produto)
);