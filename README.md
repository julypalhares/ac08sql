--Bruna Manso        RA 1900726
--Danielle Fernandes RA:1900400
--Erica Herculano    RA:1900241
--Juliana Palhares   RA:1900406 
--Pablo Kalil        RA:1900997

CREATE TABLE Usuario (
	id INT NOT NULL,
	registro VARCHAR (20) NOT NULL,
	nome VARCHAR(110) NOT NULL,
	email VARCHAR(130) NOT NULL,
	celular CHAR(10) NOT NULL,
	login VARCHAR(20) NOT NULL,
	senha VARCHAR(13) NOT NULL,
	data_expiracao DATE NOT NULL CONSTRAINT DF_data_expiracao DEFAULT ('1900-01-01'),
	CONSTRAINT pk_idUsuario PRIMARY KEY (id),
	CONSTRAINT uq_regUsuario UNIQUE (registro),
	CONSTRAINT uq_elUsuario UNIQUE (email),
	CONSTRAINT uq_lUsuario UNIQUE (login));

CREATE TABLE Professor (
	id INT NOT NULL,
	id_usuario INT NOT NULL,
	apelido VARCHAR(20) NULL,
	CONSTRAINT pk_idProf PRIMARY KEY (id),
	CONSTRAINT fk_idUsuProf FOREIGN KEY (id_usuario) REFERENCES Usuario (id));

CREATE TABLE Coordenador (
	id INT NOT NULL,
	id_usuario INT NOT NULL,
	CONSTRAINT pk_idCoor PRIMARY KEY (id),
	CONSTRAINT fk_idUcoor FOREIGN KEY (id_usuario) REFERENCES Usuario(id));

CREATE TABLE Disciplina (
	id INT NOT NULL,
	nome VARCHAR(40) NOT NULL,
	slug VARCHAR(25) NULL,
	plano_ensino VARCHAR(1000) NOT NULL,
	carga_horaria CHAR(2) NOT NULL CONSTRAINT ck_cargaHdisciplina CHECK (carga_horaria IN('50', '90')),
	competencias VARCHAR(300) NOT NULL,
	habilidades VARCHAR(300) NOT NULL,
	conteudo_programatico VARCHAR(1100) NOT NULL,
	bibliografia_basica VARCHAR(300) NOT NULL,
	bibliografia_complemetar VARCHAR(300) NOT NULL,
	percentual_pratico INT NOT NULL CONSTRAINT ck_percentualPdisciplina CHECK (percentual_pratico BETWEEN 0 AND 100), 
	percentual_teorico AS 100 - percentual_pratico,
	id_professor_responsavel INT NOT NULL,
	CONSTRAINT pk_idDisciplina PRIMARY KEY (id),
	CONSTRAINT fk_id_prof_respoDisciplina FOREIGN KEY(id_professor_responsavel) REFERENCES Professor(id),
	CONSTRAINT uq_nomeDisci UNIQUE (nome),
	CONSTRAINT uq_slugDisci UNIQUE (slug));

CREATE TABLE Curso (
	id INT NOT NULL,
	nome VARCHAR(40) NOT NULL,
	sigla VARCHAR(8) NULL,
	tipo VARCHAR(40) NULL,
	duracao VARCHAR(20) NOT NULL,
	sobre VARCHAR(800) NOT NULL,
	id_coordenador INT NOT NULL,
	CONSTRAINT pk_idCurso PRIMARY KEY (id),
	CONSTRAINT fk_id_coordeCurso FOREIGN KEY (id_coordenador) REFERENCES Coordenador (id),
	CONSTRAINT uq_nomeCurso UNIQUE (nome),
	CONSTRAINT uq_siglaCurso UNIQUE (sigla));

CREATE TABLE Periodo (
	id INT NOT NULL,
	id_curso INT NOT NULL,
	periodo VARCHAR(20) NOT NULL CONSTRAINT ck_periodo CHECK (periodo IN('matutino', 'vespertino', 'integral', 'noturno')),
	horario TIME NOT NULL,
	valor NUMERIC(20,5) NOT NULL,
	CONSTRAINT pk_idPeriodo PRIMARY KEY (id),
	CONSTRAINT fk_id_CPeriodo FOREIGN KEY (id_curso) REFERENCES Curso(id),
	CONSTRAINT uq_Periodo UNIQUE (id_curso, periodo));

CREATE TABLE Turma (
	id INT NOT NULL,
	id_curso INT NOT NULL,
	semestre TINYINT NOT NULL CONSTRAINT ck_semestreTurma CHECK (semestre IN('1', '2')), 
	letra CHAR(1) NOT NULL CONSTRAINT CK_letraTurma CHECK (letra LIKE '[A-Z]'),
	periodo VARCHAR(15) NOT NULL CONSTRAINT CK_periodoTurma CHECK (periodo IN('matutino', 'vespertino', 'integral', 'noturno')),
	CONSTRAINT pk_idTurma PRIMARY KEY (id),
	CONSTRAINT fk_id_cursoTurma FOREIGN KEY (id_curso) REFERENCES Curso(id)
	CONSTRAINT uq_Turma UNIQUE (id_curso, semestre, letra));

CREATE TABLE Aluno (
	id INT NOT NULL,
	id_usuario INT NOT NULL,
	foto VARCHAR(200) NOT NULL,
	id_turma INT NOT NULL,
	CONSTRAINT pk_idAluno PRIMARY KEY (id),
	CONSTRAINT fk_id_Ualuno FOREIGN KEY (id_usuario) REFERENCES Usuario(id),
	CONSTRAINT fk_id_Taluno FOREIGN KEY (id_turma) REFERENCES Turma(id));

CREATE TABLE DisciplinaOfertada (
	id INT NOT NULL,
	id_turma INT NOT NULL,
	id_disciplina INT NOT NULL,
	ano VARCHAR(4) NOT NULL CONSTRAINT ck_ano CHECK (ano BETWEEN '1900' and '2100'),
	semestre TINYINT NOT NULL CONSTRAINT ck_semestre CHECK (semestre IN('1', '2')),
	id_professor INT NOT NULL,
	metodologia VARCHAR (400) NOT NULL,
	recursos VARCHAR(400) NOT NULL,
	criterio_avaliacao VARCHAR(300) NOT NULL,
	CONSTRAINT pk_idDisci_Ofertada PRIMARY KEY (id),
	CONSTRAINT fk_id_Tdisciplina_Ofertada FOREIGN KEY (id_turma) REFERENCES Turma(id),
	CONSTRAINT fk_id_disci_Disci_Ofertada FOREIGN KEY (id_disciplina) REFERENCES Disciplina (id),
	CONSTRAINT fk_id_profDisciplina_Ofertada FOREIGN KEY (id_professor) REFERENCES Professor (id),
	CONSTRAINT uq_Disciplina_Ofertada UNIQUE (id_turma, id_disciplina, ano, semestre));

CREATE TABLE SolicitacaoMatricula (
	id INT NOT NULL,
	id_aluno INT NOT NULL,
	id_disciplina INT NOT NULL,
	numero INT IDENTITY (1,1) NOT NULL,
	data DATE NOT NULL CONSTRAINT DF_dataSolicitacaoMatricula DEFAULT (getdate()),
	id_coordenador INT NOT NULL,
	data_aprovacao DATE NOT NULL,
	CONSTRAINT pk_idSoli_Matricula PRIMARY KEY (id),
	CONSTRAINT fk_id_alunoSoli_Matricula FOREIGN KEY (id_aluno) REFERENCES Aluno(id),
	CONSTRAINT fk_id_disci_Soli_Matricula FOREIGN KEY (id_disciplina) REFERENCES Disciplina(id),
	CONSTRAINT fk_id_coorSoli_Matricula FOREIGN KEY (id_coordenador) REFERENCES Coordenador(id),
	CONSTRAINT uq_id_aluno_disciSoli_Matricula UNIQUE (id_aluno,id_disciplina),
	CONSTRAINT uq_numeroSoli_Matricula UNIQUE (numero));
