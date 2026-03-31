# BD-comp-natacao
Ao desenvolver estas procedures, identifiquei e corrigi os seguintes pontos críticos:

  1. Correção da "Alucinação" de Categorias (Lógica de Negócio)
Erro da IA: Inicialmente, o modelo de IA sugeriu faixas etárias genéricas (ex: Mirim começando aos 9 anos).

Correção Aplicada: Conforme o regulamento da FASE Aquática para o Troféu Alexandre Pussieldi, a lógica foi ajustada para: Pré-Mirim (Até 9 anos) e Mirim (10-11 anos).

Justificativa: Diferente de categorias escolares, a natação federada utiliza o ano de nascimento como base rígida. O cálculo foi implementado usando EXTRACT(YEAR FROM...) para garantir precisão na data de corte do evento.

2. Alinhamento com a Entidade de Agregação (RESULTADO)
Decisão Técnica: As procedures quadro_de_medalhas e atletas_e_clubes consomem dados diretamente da tabela RESULTADO.

Justificativa: Seguindo a estratégia de design do projeto, a tabela RESULTADO centraliza o desfecho final (colocação e tempo). Isso evita que as procedures precisem realizar cálculos complexos na tabela PARCIAL (granulada) apenas para gerar um ranking simples, otimizando a performance do banco.

3. Uso das Tabelas de Domínio (Lookup Tables)
Implementação: As procedures realizam JOINS com as tabelas CLUBE e ATLETA, acessando as descrições normalizadas.

Vantagem: Graças à normalização em 3ª Forma Normal, o relatório de clubes não corre o risco de duplicar agremiações por erros de digitação (ex: "Flamengo" vs "C.R. Flamengo"), pois a procedure utiliza o id_clube como âncora de integridade.

4. Integridade Referencial e Performance
Tipagem: Foi respeitado o uso de INT para chaves primárias e estrangeiras nos Joins das procedures, garantindo rapidez na indexação de milhares de resultados.

Segurança de Schema: Toda a implementação foi encapsulada no schema atividadesql2, garantindo que as funções estatísticas não entrem em conflito com outras tabelas do banco de dados global.

5. Tratamento de Dados (Casos de Borda)
Refinamento: Adicionamos o uso de FILTER e CASE WHEN para tratar atletas desclassificados ou que não compareceram (DNS). No quadro de medalhas, apenas colocações de 1 a 3 geram contagem, ignorando registros nulos ou zero, o que é uma falha comum em códigos gerados puramente por IA sem contexto esportivo.

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
