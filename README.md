# Registros_de_voo.abap
Projeto ABAP - Registros de voo

REPORT zcurso_alura.

TYPES: BEGIN OF estrutura_relatorio,
         cia_aeria  TYPE s_carr_id,
         numero_voo TYPE s_conn_id,
         data       TYPE s_date,
         preco      TYPE s_price.

TYPES END OF estrutura_relatorio.

CONSTANTS: posicao_preco_cabecalho TYPE i VALUE 36,

           posicao_n_voo_corpo     TYPE i VALUE 12,
           posicao_data_corpo      TYPE i VALUE 25,
           Coluna type c VALUE '|'.

DATA: tabela_voo    TYPE STANDARD TABLE OF estrutura_relatorio,
      estrutura_voo LIKE LINE OF tabela_voo.

START-OF-SELECTION.

  SELECT-OPTIONS: s_cia   FOR estrutura_voo-cia_aeria NO INTERVALS ,
                  s_n_voo FOR estrutura_voo-numero_voo,
                  s_data  FOR estrutura_voo-data,
                  s_preco FOR estrutura_voo-preco.
ULINE at (58) .
NEW-LINE.
FORMAT COLOR 4.
  WRITE: Coluna, text-001, Coluna,
         text-002,Coluna,
         text-003,Coluna,
         text-004, Coluna.
new-LINE.
ULINE at (58).

FORMAT COLOR OFF.

  SELECT carrid connid fldate price
     FROM sflight
     INTO TABLE tabela_voo
      WHERE carrid IN s_cia
        AND connid IN s_n_voo
        AND fldate IN s_data
        AND price IN s_preco.

  IF sy-subrc = 0.

    LOOP AT tabela_voo INTO estrutura_voo.

      PERFORM escreve_nova_linha_no_corpo USING estrutura_voo-cia_aeria
                                   estrutura_voo-numero_voo
                                   estrutura_voo-data
                                   estrutura_voo-preco.
    ENDLOOP.

    PERFORM msg_sucesso.

  ELSE .

  skip.
    MESSAGE 'Não foram encontrados nenhum resultado, favor realizar uma nova pesquisa!' TYPE 'I'.
    WRITE 'Não foram encontrados nenhum resultado, favor realizar uma nova pesquisa.'.


  ENDIF.
  new-LINE.
  ULINE At (189).
  SKIP.
  WRITE 'Relatório gerado em:'.
  WRITE sy-datum.

FORM escreve_nova_linha_no_corpo USING cia_aeria numero_voo data preco.
new-LINE.

  WRITE: Coluna, (10) cia_aeria CENTERED,Coluna,
           (15) numero_voo CENTERED,Coluna,
           (12) data DD/MM/YYYY, Coluna,
           (08) preco LEFT-JUSTIFIED, Coluna.
ENDFORM.

FORM msg_sucesso.

  DATA: numero_registro TYPE string,
          msg             TYPE string.

    numero_registro = sy-dbcnt .
    CONCATENATE 'Sucesso! Encontrados:' numero_registro 'registro(s).' INTO msg SEPARATED BY space.
FORMAT color 1.
    MESSAGE msg TYPE 'S'.
endform.
