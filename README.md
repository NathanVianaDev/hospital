# hospital
## Hospital Fundamental
Um pequeno hospital local busca desenvolver um novo sistema que atenda melhor às suas necessidades. Atualmente, parte da operação ainda se apoia em planilhas e arquivos antigos, mas espera-se que esses dados sejam transferidos para o novo sistema assim que ele estiver funcional. Neste momento, é necessário analisar com cuidado as necessidades desse cliente e sugerir uma estrutura de banco de dados adequada por meio de um Diagrama Entidade-Relacionamento.

## Estudo do caso

 O hospital necessita de um sistema para sua área clínica que ajude a controlar consultas realizadas. Os médicos podem ser generalistas, especialistas ou residentes e têm seus dados pessoais cadastrados em planilhas digitais. Cada médico pode ter uma ou mais especialidades, que podem ser pediatria, clínica geral, gastroenterologia e dermatologia. Alguns registros antigos ainda estão em formulário de papel, mas será necessário incluir esses dados no novo sistema.

Os pacientes também precisam de cadastro, contendo dados pessoais (nome, data de nascimento, endereço, telefone e e-mail), documentos (CPF e RG) e convênio. Para cada convênio, são registrados nome, CNPJ e tempo de carência.

As consultas também têm sido registradas em planilhas, com data e hora de realização, médico responsável, paciente, valor da consulta ou nome do convênio, com o número da carteira. Também é necessário indicar na consulta qual a especialidade buscada pelo paciente.

Deseja-se ainda informatizar a receita do médico, de maneira que, no encerramento da consulta, ele possa registrar os medicamentos receitados, a quantidade e as instruções de uso. A partir disso, espera-se que o sistema imprima um relatório da receita ao paciente ou permita sua visualização via internet.
![hospital](https://github.com/ErickGabrielDev/hospital/assets/159559836/92337f27-8a92-45a4-895e-c98bc822c1be)

## O segredo do Hospital 
Após a primeira versão do projeto de banco de dados para o sistema hospitalar, notou-se a necessidade de expansão das funcionalidades, incluindo alguns requisitos essenciais a essa versão do software. As funcionalidades em questão são para o controle na internação de pacientes. Será necessário expandir o Modelo ER desenvolvido e montar o banco de dados, criando as tabelas para o início dos testes.

## Estudo de caso
Considere a seguinte descrição e o diagrama ER abaixo:

No hospital, as internações têm sido registradas por meio de formulários eletrônicos que gravam os dados em arquivos.

Para cada internação, são anotadas a data de entrada, a data prevista de alta e a data efetiva de alta, além da descrição textual dos procedimentos a serem realizados.

As internações precisam ser vinculadas a quartos, com a numeração e o tipo.

Cada tipo de quarto tem sua descrição e o seu valor diário (a princípio, o hospital trabalha com apartamentos, quartos duplos e enfermaria).

Também é necessário controlar quais profissionais de enfermaria estarão responsáveis por acompanhar o paciente durante sua internação. Para cada enfermeiro(a), é necessário nome, CPF e registro no conselho de enfermagem (CRE).

A internação, obviamente, é vinculada a um paciente – que pode se internar mais de uma vez no hospital – e a um único médico responsável.
![hospital2](https://github.com/ErickGabrielDev/hospital/assets/159559836/abdfdea8-7e32-41c1-a930-b34e7c7420cd)

## Código SQL

```sql
create database hospital; use hospital;

create table medico ( codigo_medico int primary key, nome_medico varchar(50) not null, cpf_medico varchar(14) unique not null, rg_medico varchar(9) unique not null, cargo_medico varchar(20) not null, data_nasc_medico date not null ); alter table medico modify cpf_medico varchar(11) unique not null;

create table paciente ( codigo_paciente int primary key, nome_paciente varchar(50) not null, cpf_paciente varchar(11) unique not null, rg_paciente varchar(9) unique not null, telefone_paciente varchar(11) not null, email_paciente varchar(50) unique not null, data_nasc_paciente date not null, codigo_convenio int not null );

alter table paciente change codigo_cargo codigo_convenio int not null;

create table convenio ( numero_carteira int primary key, nome_convenio varchar(50) not null, tempo_carencia varchar(10) not null, cnpj_convenio varchar(14) unique not null );

alter table paciente add foreign key fk_codigo_convenio (codigo_convenio) references convenio(numero_carteira);

create table consulta( codigo_consulta int primary key, data_consulta date not null, horario_consulta time not null, valor_consulta decimal(5,2) not null, forma_pagamento varchar(20) not null, codigo_paciente int not null, codigo_medico int not null, codigo_especialidade int not null );

create table especialidade( codigo_especialidade int primary key, descricao_especialidade varchar(50) not null );

create table especialidade_medico( codigo_especialidade_medico int primary key, codigo_medico int not null, codigo_paciente int not null );

alter table consulta add foreign key fk_codigo_especialidade (codigo_especialidade) references especialidade(codigo_especialidade);



alter table especialidade_medico add foreign key fk_codigo_medico (codigo_medico) references medico(codigo_medico);

alter table especialidade_medico add foreign key fk_codigo_paciente (codigo_paciente) references paciente(codigo_paciente);
```

# O Prisioneiro dos Dados
Com o banco de dados para o sistema hospitalar completamente montado, é necessário incluir dados para realizar os devidos testes e validar sua viabilidade quanto ao sistema. Nesta etapa, também é importante realizar a separação de alguns scripts iniciais para o banco, com os dados que serão necessários a um povoamento inicial do sistema.

- Inclua ao menos dez médicos de diferentes especialidades. Ao menos sete especialidades (considere a afirmação de que “entre as especialidades há pediatria, clínica geral, gastrenterologia e dermatologia”).

```sql
  -- Inserção de dados na tabela especialidade
INSERT INTO especialidade (codigo_especialidade, descricao_especialidade)
VALUES
(1, 'Cardiologia'),
(2, 'Pediatria'),
(3, 'Clínica Geral'),
(4, 'Ortopedia'),
(5, 'Dermatologia'),
(6, 'Psiquiatria'),
(7, 'Ginecologia'),
(8, 'Oftalmologia'),
(9, 'Gastroenterologia'),
(10, 'Oncologia');

-- Inserção de dados na tabela medico com dados modificados
INSERT INTO medico (codigo_medico, nome_medico, cpf_medico, rg_medico, crm_medico, cargo_medico, data_nasc_medico, codigo_especialidade)
VALUES
(1, 'Dr. Marcos Andrade', '321.654.987-00', 'MG7654321', 'CRM54321/MG', 'Cardiologista', '1976-05-16', 1),
(2, 'Dra. Carla Mendes', '432.765.098-11', 'SP8765432', 'CRM65432/SP', 'Pediatra', '1981-07-21', 2),
(3, 'Dr. Felipe Santos', '543.876.109-22', 'RJ9876543', 'CRM76543/RJ', 'Neurologista', '1979-10-11', 3),
(4, 'Dra. Beatriz Souza', '654.987.210-33', 'PR0987654', 'CRM87654/PR', 'Ortopedista', '1986-12-26', 4),
(5, 'Dr. Rafael Costa', '765.098.321-44', 'RS1098765', 'CRM98765/RS', 'Dermatologista', '1971-01-31', 5),
(6, 'Dra. Juliana Rocha', '876.109.432-55', 'SC2109876', 'CRM09876/SC', 'Psiquiatra', '1983-04-19', 6),
(7, 'Dr. Gabriel Oliveira', '987.210.543-66', 'BA3210987', 'CRM10987/BA', 'Ginecologista', '1974-08-23', 7),
(8, 'Dra. Larissa Dias', '098.321.654-77', 'DF4321098', 'CRM21098/DF', 'Oftalmologista', '1989-06-15', 8),
(9, 'Dr. Eduardo Lima', '109.432.765-88', 'CE5432109', 'CRM32109/CE', 'Gastroenterologista', '1977-11-06', 9),
(10, 'Dra. Vanessa Martins', '210.543.876-99', 'GO6543210', 'CRM43210/GO', 'Oncologista', '1982-09-20', 10);
```
- Inclua ao menos 15 pacientes.
```sql
-- Inserção de dados na tabela convenio com dados modificados
INSERT INTO convenio (numero_carteira, nome_convenio, tempo_carencia, cnpj_convenio)
VALUES
(1, 'SaúdePlus', '40 dias', '11002233000111'),
(2, 'Vida Ampla', '50 dias', '22003344000222'),
(3, 'BemEstar Saúde', '35 dias', '33004455000333'),
(4, 'Seguro Saúde', '25 dias', '44005566000444'),
(5, 'SaúdeTotal', '55 dias', '55006677000555');

-- Inserção de dados na tabela paciente com dados modificados
INSERT INTO paciente (codigo_paciente, nome_paciente, cpf_paciente, rg_paciente, telefone_paciente, email_paciente, data_nasc_paciente, numero_carteira_convenio)
VALUES
(1, 'Ana Costa', '98765432101', 'MG7654321', '31987654321', 'ana@example.com', '1991-01-11', 1),
(2, 'Breno Santos', '87654321012', 'SP8765432', '11987654322', 'breno@example.com', '1986-02-12', 2),
(3, 'Clara Nunes', '76543210923', 'RJ9876543', '21987654323', 'clara@example.com', '1996-03-13', 3),
(4, 'Diego Alves', '65432109834', 'PR0987654', '41987654324', 'diego@example.com', '1981-04-14', 4),
(5, 'Elena Rocha', '54321098745', 'RS1098765', '51987654325', 'elena@example.com', '1976-05-15', 5),
(6, 'Fábio Martins', '43210987656', 'SC2109876', '61987654326', 'fabio@example.com', '1989-06-16', 1),
(7, 'Giselle Lima', '32109876567', 'BA3210987', '71987654327', 'giselle@example.com', '1980-07-17', 2),
(8, 'Henrique Pereira', '21098765478', 'DF4321098', '81987654328', 'henrique@example.com', '1993-08-18', 3),
(9, 'Isabela Carvalho', '10987654389', 'CE5432109', '91987654329', 'isabela@example.com', '1984-09-19', 4),
(10, 'Júlio Rezende', '09876543290', 'GO6543210', '61987654330', 'julio@example.com', '1992-10-20', 5),
(11, 'Lívia Teixeira', '98765098765', 'MA1234509', '71987654331', 'livia@example.com', '1988-11-21', 1),
(12, 'Marcelo Farias', '87654109876', 'PA2345610', '81987654332', 'marcelo@example.com', '1983-12-22', 2),
(13, 'Natalia Monteiro', '76543210987', 'PE3456721', '91987654333', 'natalia@example.com', '1994-01-23', 3),
(14, 'Otávio Silva', '65432109898', 'PI4567832', '61987654334', 'otavio@example.com', '1995-02-24', 4),
(15, 'Patrícia Ramos', '54321098709', 'RJ5678943', '31987654335', 'patricia@example.com', '1991-03-25', 5);

-- Inserção de dados na tabela endereco com dados modificados
INSERT INTO endereco (codigo_endereco, rua, bairro, cep, numero, complemento, codigo_paciente)
VALUES
(1, 'Rua Alfa', 'Bairro Alfa', '30111111', 201, 'Apto 101', 1),
(2, 'Rua Beta', 'Bairro Beta', '40122222', 202, 'Apto 102', 2),
(3, 'Rua Gama', 'Bairro Gama', '50133333', 203, 'Apto 103', 3),
(4, 'Rua Delta', 'Bairro Delta', '60144444', 204, 'Apto 104', 4),
(5, 'Rua Épsilon', 'Bairro Épsilon', '70155555', 205, 'Apto 105', 5),
(6, 'Rua Zeta', 'Bairro Zeta', '80166666', 206, 'Apto 106', 6),
(7, 'Rua Eta', 'Bairro Eta', '90177777', 207, 'Apto 107', 7),
(8, 'Rua Teta', 'Bairro Teta', '01188888', 208, 'Apto 108', 8),
(9, 'Rua Iota', 'Bairro Iota', '11199999', 209, 'Apto 109', 9),
(10, 'Rua Kapa', 'Bairro Kapa', '12100000', 210, 'Apto 110', 10),
(11, 'Rua Lambda', 'Bairro Lambda', '13111111', 211, 'Apto 111', 11),
(12, 'Rua Mi', 'Bairro Mi', '14122222', 212, 'Apto 112', 12),
(13, 'Rua Ni', 'Bairro Ni', '15133333', 213, 'Apto 113', 13),
(14, 'Rua Xi', 'Bairro Xi', '16144444', 214, 'Apto 114', 14),
(15, 'Rua Omicron', 'Bairro Omicron', '17155555', 215, 'Apto 115', 15);
```

- Registre 20 consultas de diferentes pacientes e diferentes médicos (alguns pacientes realizam mais que uma consulta). As consultas devem ter ocorrido entre 01/01/2015 e 01/01/2022. Ao menos dez consultas devem ter receituário com dois ou mais medicamentos.

```sql
-- Inserção de dados na tabela consulta com dados modificados
INSERT INTO consulta (codigo_consulta, data_consulta, horario_consulta, valor_consulta, forma_pagamento, codigo_paciente, codigo_medico, codigo_especialidade, numero_carteira_convenio)
VALUES
(1, '2015-01-15', '09:00:00', 150.00, 'Transferência', 1, 1, 1, 1),
(2, '2015-02-20', '10:30:00', 200.00, 'PIX', 2, 2, 2, 2),
(3, '2016-03-25', '11:00:00', 175.00, 'Boleto', 3, 3, 3, 3),
(4, '2016-04-10', '08:45:00', 180.00, 'Cartão', 4, 4, 4, 4),
(5, '2017-05-05', '14:00:00', 160.00, 'Dinheiro', 5, 5, 5, 5),
(6, '2017-06-15', '15:30:00', 190.00, 'Transferência', 6, 6, 6, 1),
(7, '2018-07-20', '09:15:00', 170.00, 'PIX', 7, 7, 7, 2),
(8, '2018-08-25', '10:00:00', 165.00, 'Boleto', 8, 8, 8, 3),
(9, '2019-09-30', '11:30:00', 185.00, 'Cartão', 9, 9, 9, 4),
(10, '2019-10-15', '13:00:00', 175.00, 'Dinheiro', 10, 10, 10, 5),
(11, '2020-01-10', '14:15:00', 200.00, 'Transferência', 11, 1, 1, 1),
(12, '2020-02-15', '15:45:00', 150.00, 'PIX', 12, 2, 2, 2),
(13, '2020-03-20', '16:00:00', 170.00, 'Boleto', 13, 3, 3, 3),
(14, '2020-04-25', '17:30:00', 180.00, 'Cartão', 14, 4, 4, 4),
(15, '2021-05-30', '08:30:00', 160.00, 'Dinheiro', 15, 5, 5, 5),
(16, '2021-06-05', '09:45:00', 190.00, 'Transferência', 1, 6, 6, 1),
(17, '2021-07-10', '10:00:00', 165.00, 'PIX', 2, 7, 7, 2),
(18, '2021-08-15', '11:15:00', 175.00, 'Boleto', 3, 8, 8, 3),
(19, '2021-09-20', '12:00:00', 185.00, 'Cartão', 4, 9, 9, 4),
(20, '2022-01-01', '13:45:00', 150.00, 'Dinheiro', 5, 10, 10, 5);

-- Inserção de dados na tabela receita com dados modificados
INSERT INTO receita (codigo_receita, nome_medicamento, qtd_medicamento, instrucoes_medicamento, codigo_consulta)
VALUES
(1, 'Ibuprofeno', 2, 'Tomar 1 comprimido a cada 6 horas', 1),
(2, 'Paracetamol', 3, 'Tomar 1 comprimido a cada 8 horas', 2),
(3, 'Amoxicilina', 5, 'Tomar 1 cápsula a cada 12 horas por 7 dias', 3),
(4, 'Losartana', 4, 'Tomar 1 comprimido ao dia', 4),
(5, 'Omeprazol', 2, 'Tomar 1 cápsula ao dia', 5),
(6, 'Metformina', 3, 'Tomar 1 comprimido 2 vezes ao dia', 6),
(7, 'Lorazepam', 2, 'Tomar 1 comprimido ao dormir', 7),
(8, 'Diclofenaco', 6, 'Tomar 1 comprimido a cada 8 horas', 8),
(9, 'Cetoconazol', 7, 'Aplicar o creme na área afetada 2 vezes ao dia', 9),
(10, 'Azitromicina', 3, 'Tomar 1 comprimido ao dia por 3 dias', 10),
(11, 'Atenolol', 3, 'Tomar 1 comprimido ao dia', 11),
(12, 'Prednisona', 2, 'Tomar 1 comprimido ao dia', 12),
(13, 'Aspirina', 4, 'Tomar 1 comprimido a cada 12 horas', 13),
(14, 'Clonazepam', 5, 'Tomar 1 comprimido ao dormir', 14),
(15, 'Sinvastatina', 3, 'Tomar 1 comprimido ao dia', 15),
(16, 'Nimesulida', 4, 'Tomar 1 comprimido a cada 8 horas', 16),
(17, 'Levotiroxina', 2, 'Tomar 1 comprimido ao dia', 17),
(18, 'Alprazolam', 4, 'Tomar 1 comprimido ao dormir', 18),
(19, 'Pantoprazol', 3, 'Tomar 1 comprimido ao dia', 19),
(20, 'Fluconazol', 2, 'Tomar 1 comprimido ao dia por 7 dias', 20);

INSERT INTO receita (codigo_receita, nome_medicamento, qtd_medicamento, instrucoes_medicamento, codigo_consulta)
VALUES
(21, 'Ibuprofeno', 1, 'Tomar 1 comprimido a cada 6 horas', 21),
(22, 'Paracetamol', 2, 'Tomar 1 comprimido ao dia', 22),
(23, 'Amoxicilina', 3, 'Tomar 1 cápsula a cada 12 horas por 7 dias', 23),
(24, 'Losartana', 5, 'Tomar 1 comprimido ao dia', 24),
(25, 'Omeprazol', 5, 'Tomar 1 cápsula ao dia', 25),
(26, 'Metformina', 4, 'Tomar 1 comprimido 2 vezes ao dia', 26),
(27, 'Lorazepam', 3, 'Tomar 1 comprimido ao dormir', 27),
(28, 'Diclofenaco', 10, 'Tomar 1 comprimido a cada 8 horas', 28),
(29, 'Cetoconazol', 9, 'Aplicar o creme na área afetada 2 vezes ao dia', 29),
(30, 'Azitromicina', 7, 'Tomar 1 comprimido ao dia por 3 dias', 30),
(31, 'Atenolol', 2, 'Tomar 1 comprimido ao dia', 31),
(32, 'Prednisona', 3, 'Tomar 1 comprimido ao dia', 32),
(33, 'Aspirina', 6, 'Tomar 1 comprimido a cada 12 horas', 33),
(34, 'Clonazepam', 6, 'Tomar 1 comprimido ao dormir', 34),
(35, 'Sinvastatina', 3, 'Tomar 1 comprimido ao dia', 35),
(36, 'Nimesulida', 3, 'Tomar 1 comprimido a cada 8 horas', 36),
(37, 'Levotiroxina', 2, 'Tomar 1 comprimido ao dia', 37),
(38, 'Alprazolam', 8, 'Tomar 1 comprimido ao dormir', 38),
(39, 'Pantoprazol', 8, 'Tomar 1 comprimido ao dia', 39),
(40, 'Fluconazol', 2, 'Tomar 1 comprimido ao dia por 7 dias', 40);

-- Inserção de dados na tabela consulta com dados modificados
INSERT INTO consulta (codigo_consulta, data_consulta, horario_consulta, valor_consulta, forma_pagamento, codigo_paciente, codigo_medico, codigo_especialidade, numero_carteira_convenio) 
VALUES
(21, '2022-02-01', '09:00:00', 160.00, 'Transferência', 1, 1, 1, NULL),
(22, '2022-02-10', '11:00:00', 210.00, 'PIX', 2, 2, 2, NULL),
(23, '2022-03-01', '13:00:00', 180.00, 'Boleto', 3, 3, 3, NULL),
(24, '2022-03-15', '15:00:00', 190.00, 'Dinheiro', 4, 4, 4, NULL);

-- Inserção de dados na tabela receita com dados modificados
INSERT INTO receita (codigo_receita, nome_medicamento, qtd_medicamento, instrucoes_medicamento, codigo_consulta) 
VALUES
(41, 'Dipirona', 2, 'Tomar 1 comprimido a cada 6 horas', 21),
(42, 'Vitamina D', 1, 'Tomar 1 comprimido ao dia', 22),
(43, 'Antialérgico', 2, 'Tomar 1 comprimido ao dia', 23),
(44, 'Ibuprofeno', 4, 'Tomar 1 comprimido a cada 8 horas', 24);
```

- Associe ao menos cinco pacientes a cinco consultas
```sql
-- Inserção de dados na tabela consulta com dados modificados
INSERT INTO consulta (codigo_consulta, data_consulta, horario_consulta, valor_consulta, forma_pagamento, codigo_paciente, codigo_medico, codigo_especialidade, codigo_convenio) 
VALUES
(1, '2022-01-15', '10:00:00', 210.00, 'Transferência Bancária', 1, 1, 1, 1),
(2, '2021-03-20', '14:00:00', 160.00, 'PIX', 2, 2, 2, 2),
(3, '2023-07-10', '09:30:00', 190.00, 'Boleto Bancário', 3, 3, 3, 3),
(4, '2020-05-25', '11:00:00', 230.00, 'Dinheiro', 4, 4, 4, 4),
(5, '2019-11-30', '16:00:00', 180.00, 'Cartão de Crédito', 5, 5, 5, 5);
```
- Registre ao menos sete internações. Pelo menos dois pacientes devem ter se internado mais de uma vez. Ao menos três quartos devem ser cadastrados. As internações devem ter ocorrido entre 01/01/2015 e 01/01/2022. Considerando que “a princípio o hospital trabalha com apartamentos, quartos duplos e enfermaria”, inclua ao menos esses três tipos com valores diferentes. Inclua dados de dez profissionais de enfermaria. Associe cada internação a ao menos dois enfermeiros.
```sql
-- Inserção de dados na tabela tipo_quarto com dados modificados
INSERT INTO tipo_quarto (codigo_tipo_quarto, diaria_quarto, descricao_quarto) VALUES
(1, 350.00, 'Suite Luxo'),
(2, 200.00, 'Quarto Triplo'),
(3, 120.00, 'Enfermaria Coletiva');

-- Inserção de dados na tabela quarto com dados modificados
INSERT INTO quarto (numero_quarto, codigo_tipo_quarto) VALUES
(201, 1),
(202, 2),
(203, 3);

-- Inserção de dados na tabela internacao com dados modificados
INSERT INTO internacao (codigo_internacao, data_prevista_alta, data_entrada, data_alta, codigo_medico, numero_quarto, codigo_paciente, numero_carteira_convenio) VALUES
(1, '2021-01-15', '2021-01-05', '2021-01-15', 1, 201, 1, 1),
(2, '2021-02-20', '2021-02-10', '2021-02-20', 2, 202, 2, 2),
(3, '2020-05-10', '2020-05-02', '2020-05-10', 3, 203, 3, 3),
(4, '2019-07-15', '2019-07-05', '2019-07-15', 4, 201, 4, 4),
(5, '2018-09-25', '2018-09-15', '2018-09-25', 5, 202, 5, 5),
(6, '2017-10-20', '2017-10-05', '2017-10-20', 6, 203, 1, 1),
(7, '2016-12-30', '2016-12-20', '2016-12-30', 7, 201, 2, 2);

-- Inserção de dados na tabela procedimento com dados modificados
INSERT INTO procedimento (codigo_procedimento, nome_procedimento, descricao_procedimento) VALUES
(1, 'Cirurgia Cardíaca Avançada', 'Cirurgia para correção de problemas cardíacos graves'),
(2, 'Hemograma Completo', 'Coleta e análise detalhada de amostras de sangue'),
(3, 'Tomografia Computadorizada', 'Imagem detalhada para diagnóstico'),
(4, 'Endoscopia Digestiva Alta', 'Exame detalhado do trato gastrointestinal superior'),
(5, 'Radioterapia', 'Tratamento para câncer utilizando radiação');

-- Inserção de dados na tabela procedimento_internacao com dados modificados
INSERT INTO procedimento_internacao (codigo_internacao, codigo_procedimento) VALUES
(1, 1),
(1, 2),
(2, 3),
(2, 4),
(3, 5),
(4, 1),
(5, 2),
(6, 3),
(7, 4);

-- Inserção de dados na tabela enfermeiro com dados modificados
INSERT INTO enfermeiro (codigo_enfermeiro, nome_enfermeiro, rg_enfermeiro, cpf_enfermeiro, cre_enfermeiro, data_nasc_enfermeiro) VALUES
(1, 'Enf. João da Costa', 'MG9876543', '33322211100', 'CRE12345/MG', '1985-01-01'),
(2, 'Enf. Maria de Lima', 'SP8765432', '44433322211', 'CRE23456/SP', '1990-02-02'),
(3, 'Enf. Pedro da Silva', 'RJ7654321', '55544433322', 'CRE34567/RJ', '1982-03-03'),
(4, 'Enf. Ana dos Santos', 'PR6543210', '66655544433', 'CRE45678/PR', '1975-04-04'),
(5, 'Enf. Carla Nunes', 'RS5432109', '77766655544', 'CRE56789/RS', '1983-05-05'),
(6, 'Enf. Bruno Souza', 'SC4321098', '88877766655', 'CRE67890/SC', '1991-06-06'),
(7, 'Enf. Paula Ribeiro', 'BA3210987', '99988877766', 'CRE78901/BA', '1974-07-07'),
(8, 'Enf. Luiz Martins', 'DF2109876', '11199988877', 'CRE89012/DF', '1987-08-08'),
(9, 'Enf. Fernanda Almeida', 'CE1098765', '22211199988', 'CRE90123/CE', '1976-09-09'),
(10, 'Enf. Ricardo Gonçalves', 'GO0987654', '33322211199', 'CRE01234/GO', '1992-10-10');

-- Inserção de dados na tabela enfermeiro_internacao com dados modificados
INSERT INTO enfermeiro_internacao (codigo_enfermeiro, codigo_internacao) VALUES
(1, 1),
(2, 1),
(3, 2),
(4, 2),
(5, 3),
(6, 3),
(7, 4),
(8, 4),
(9, 5),
(10, 5),
(1, 6),
(2, 6),
(3, 7),
(4, 7);

-- Inserção de dados na tabela internacao com novos dados
INSERT INTO internacao (codigo_internacao, data_prevista_alta, data_entrada, data_alta, codigo_medico, numero_quarto, codigo_paciente, numero_carteira_convenio) VALUES
(13, '2022-01-12', '2022-01-02', '2022-01-12', 1, 201, 1, 1),
(14, '2022-02-18', '2022-02-05', '2022-02-18', 2, 202, 2, 2),
(15, '2022-03-25', '2022-03-10', '2022-03-25', 3, 203, 3, 3),
(16, '2022-04-28', '2022-04-10', '2022-04-28', 4, 204, 4, 4);
```
# A Ordem do Alterar. 
Um banco de dados pode sofrer alterações ao longo da sua concepção e do seu desenvolvimento. Nesse momento devemos nos preparar para atualizar nossas estratégias. 
Pensando no banco que já foi criado para o Projeto do Hospital, realize algumas alterações nas tabelas e nos dados usando comandos de atualização e exclusão:
Crie um script que adicione uma coluna “em_atividade” para os médicos, indicando se ele ainda está atuando no hospital ou não. 
Crie um script para atualizar ao menos dois médicos como inativos e os demais em atividade.

```sql
alter table medico add column em_atividade varchar(10);

update medico
set em_atividade = "inativo"
where codigo_medico between 1 and 2;

update medico
set em_atividade = "ativo"
where codigo_medico between 3 and 10;

select * from medico;
```

# As Relíquias dos Dados
Uma vez que o banco estiver bem estruturado e desenhado, é possível realizar testes, simulando relatórios ou telas que o sistema possa necessitar. A tarefa consiste em criar consultas que levem aos resultados esperados.

- 1. Todos os dados e o valor médio das consultas do ano de 2020 e das que foram feitas sob convênio.
```sql
select * from consulta where year(data_consulta) = 2020 and numero_carteira_convenio is not null;
```

- 2. Todos os dados das internações que tiveram data de alta maior que a data prevista para a alta.
```sql
select * from internacao where data_alta > data_prevista_alta;
```

- 3. Receituário completo da primeira consulta registrada com receituário associado.
```sql
select * 
from receita as r
inner join consulta as c
on r.codigo_consulta = c.codigo_consulta
order by data_consulta asc
limit 1;
```

- 4. Todos os dados da consulta de maior valor e também da de menor valor (ambas as consultas não foram realizadas sob convênio).
```sql
select * from consulta where valor_consulta = (select max(valor_consulta) from consulta) or valor_consulta = (select min(valor_consulta) from consulta);
```

- 5. Todos os dados das internações em seus respectivos quartos, calculando o total da internação a partir do valor de diária do quarto e o número de dias entre a entrada e a alta.
```sql
select i.data_prevista_alta, i.data_entrada, i.data_alta, q.numero_quarto, tq.descricao_quarto, tq.diaria_quarto * datediff(i.data_alta, i.data_entrada) as total_internacao
from internacao as i
inner join quarto as q
on i.numero_quarto = q.numero_quarto
inner join tipo_quarto as tq
on q.codigo_tipo_quarto = tq.codigo_tipo_quarto;
```

- 6. Data, procedimento e número de quarto de internações em quartos do tipo “apartamento”.
```sql
select i.data_entrada, p.descricao_procedimento, q.numero_quarto, tq.descricao_quarto
from internacao as i
inner join procedimento_internacao ip
on ip.codigo_internacao = i.codigo_internacao
inner join procedimento p
on ip.codigo_procedimento = p.codigo_procedimento
inner join quarto as q
on i.numero_quarto = q.numero_quarto
inner join tipo_quarto as tq
on tq.codigo_tipo_quarto = q.codigo_tipo_quarto
where tq.descricao_quarto = 'Apartamento';
```

- 7. Nome do paciente, data da consulta e especialidade de todas as consultas em que os pacientes eram menores de 18 anos na data da consulta e cuja especialidade não seja “pediatria”, ordenando por data de realização da consulta.
 
- 8. Nome do paciente, nome do médico, data da internação e procedimentos das internações realizadas por médicos da especialidade “gastroenterologia”, que tenham acontecido em “enfermaria”.

- 9. Os nomes dos médicos, seus CRMs e a quantidade de consultas que cada um realizou.
```sql
select nome_medico, crm_medico, count(codigo_consulta) as numero_consultas
from medico as m
inner join consulta as c on m.codigo_medico = c.codigo_medico group by nome_medico, crm_medico;
```

- 10. Todos os médicos que tenham "Gabriel" no nome.
```sql
select * from medico where nome_medico = 'Gabriel';
```

- 11. Os nomes, CREs e número de internações de enfermeiros que participaram de mais de uma internação.
```sql
select nome_enfermeiro, cre_enfermeiro, count(i.codigo_internacao) as internacoes
from enfermeiro as e
inner join enfermeiro_internacao as ei
on e.codigo_enfermeiro = ei.codigo_enfermeiro
inner join internacao as i 
on i.codigo_internacao = ei.codigo_internacao
group by nome_enfermeiro, cre_enfermeiro;
```

