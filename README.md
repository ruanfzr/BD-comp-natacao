# BD-comp-natacao
diagrama da estrutura de um sistema de gestão de competições de natação
ESTADO: Armazena as unidades federativas (sigla e nome) para localização dos clubes.
CLUBE: Entidade que representa as agremiações esportivas filiadas.
ATLETA: Cadastro principal dos nadadores, contendo dados pessoais e registros federativos (CBDA).
CATEGORIA: Define as divisões por idade (ex: Juvenil) com faixas etárias mínimas e máximas.
SEXO: Tabela auxiliar para padronização de gênero (Masculino/Feminino).
EVENTO: O torneio ou campeonato em si, com informações de local e tipo de piscina (curta ou longa).
PROVA: A unidade técnica da competição (ex: 50m Borboleta), vinculada a um estilo e categoria.
ESTILO: Define o tipo de nado (Crawl, Costas, Peito, Borboleta ou Medley).
SERIE: Divisão das provas em baterias para organizar as largadas dos atletas.
RESULTADO_PARCIAL: Registro de tempos intermediários e passagens durante a execução da prova.
RESULTADO: O registro final oficial, consolidando o tempo total e a colocação do atleta.

```mermaid
---
config:
  theme: neo
  layout: dagre
---
erDiagram
	direction TB
    ESTADO {
		int id_estado PK ""  
		string sigla UK ""  
		string nome_estado  ""   
	}

	CLUBE {
		int id_clube PK ""  
		string nome_clube  ""  
		string sigla  ""  
		string federacao  ""  
	}

	ATLETA {
		int id_atleta PK ""  
		string nome_atleta  ""  
		date data_nascimento  ""  
		string registro_cbda  ""  
		int id_clube FK ""  
		int id_categoria FK ""  
		int id_sexo FK ""  
	}

	CATEGORIA {
		int id_categoria PK ""  
		string nome_categoria  ""  
		int idade_minima  ""  
		int idade_maxima  ""  
	}

	PROVA {
		int id_prova PK ""  
		string descricao  ""  
		string estilo  ""  
		int distancia  ""  
		int id_categoria FK ""  
		int id_evento FK ""  
	}

    ESTILO {
		int id_estilo PK ""  
		string nome_estilo UK  ""  
	}

	SERIE {
		int id_prova PK ""  
		string descricao  ""  
		string estilo  ""  
		int distancia  ""  
		int id_categoria FK ""  
		int id_evento FK ""  
	}

	RESULTADO {
		int id_resultado PK ""  
		string tempo_final  ""  
		int colocacao  ""  
		int id_atleta FK ""  
		int id_prova FK ""  
	}

	EVENTO {
		int id_evento PK ""  
		string nome_evento  ""  
		string local  ""  
		string tipo_piscina  ""  
	}

	RESULTADO_PARCIAL {
		int id_atleta PK ""  
		string nome_atleta  ""  
		float tempo_m  ""  
		string registro_cbda  ""  
		int id_clube FK ""  
		int id_categoria FK ""  
		int id_sexo FK ""  
	}

	SEXO {
		int id_sexo PK ""  
		string descricao  "1-Masculino, 2-Feminino"  
	}

	CLUBE||--o{ATLETA:"vincula"
	CATEGORIA||--o{ATLETA:"classifica"
	CATEGORIA||--o{PROVA:"define"
	ATLETA||--o{RESULTADO_PARCIAL:"registra"
	SEXO||--o{ATLETA:"define"
	PROVA||--o{RESULTADO_PARCIAL:"gera" 
    PROVA||--o{SERIE:"gera"
    ESTILO||--o{PROVA:"caracteriza"
	EVENTO||--o{PROVA:"contem"
	RESULTADO_PARCIAL||--o{RESULTADO:"registra"
    ESTADO||--o{CLUBE:"sedia"
    

	style SERIE :stroke-width:2px,stroke-dasharray: 0
