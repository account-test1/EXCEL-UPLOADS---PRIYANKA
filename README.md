# EXCEL-UPLOADS---PRIYANKA
EXCEL UPLOADS - PRIYANKA
******************************************BEHAVIOR DEFINITION**************************************************************************************
managed implementation in class zbp_ops_i_post_prod unique;
strict;
with draft;

define behavior for ZOPS_I_POST_PROD alias POST_PROD
persistent table zops_post_prod
draft table zops_post_prod_d query zops_i_post_prod_d
lock master total etag Lastchangedat
authorization master ( instance )
etag master Locallastchangedat
{
  field ( mandatory : create, readonly : update ) ZBATCH, ZVIALNUM;
  field ( readonly ) Createdby, Createdon, Lastmodifiedby, Lastchangedat, Locallastchangedat;
  create;
  update;
  delete;
  action Upload parameter z_fileupload_ae result [1] $self;
  draft action Edit;
  draft action Activate;
  draft action Discard;
  draft action Resume;
  draft determine action Prepare;

  mapping for zops_post_prod
  {
    Zbatch = zbatch;
    Zvialnum = zvialnum;
    Zkdauf = zkdauf;
    Zcompany = zcompany;
    Zpmbqnty = zpmbqnty;
    Zrmbqnty = zrmbqnty;
    Zmatnr = zmatnr;
    Zitem = zitem;
    Zplant = zplant;
    Zphour = zphour;
    Zthour = zthour;
    Zamci = zamci;
    Zinjdat = zinjdat;
    Zimpstat = zimpstat;
    Zvialstat = zvialstat;
    Zvialqnty = zvialqnty;
    Zvolact = zvolact;
    Zacttc = zacttc;
    Zcaldat = zcaldat;
    Zexpdattim = zexpdattim;
    Zshpdattim = zshpdattim;
    Zdosenum = zdosenum;
    Ztimezone = ztimezone;
    Ztrdocnum = ztrdocnum;
    Zsalqty = zsalqty;
    Zsot = zsot;
    zdispstatus = zdispstatus;
    Zimporteddatetime = zimporteddatetime;
    createdby = Createdby;
    createdon = Createdon;
    lastmodifiedby = Lastmodifiedby;
    lastchangedat = Lastchangedat;
    locallastchangedat = Locallastchangedat;
  }
}
****************************************************************************************************************************************
************************************************************BEHAVIOR POOL CLASS****************************************************************
CLASS lhc_post_prod DEFINITION INHERITING FROM cl_abap_behavior_handler FINAL.
*----------------------------------------------------------------------*
* Development ID: ZDDE-00106697                                        *
* Functional  ID: ZFSE-00106696                                        *
* Description:Implementation Class for production table                *
*----------------------------------------------------------------------*
*  Change Log:                                                         *
* ---------------------------------------------------------------------*
*    Who       Date           CR            Text                       *
*  KOPPIKR2  17-Dec-2025   1100282721   Initial version                *
*----------------------------------------------------------------------*
  CLASS-DATA: mt_dbtable TYPE STANDARD TABLE OF zops_post_prod WITH EMPTY KEY.

  PRIVATE SECTION.
    METHODS get_instance_authorizations FOR INSTANCE AUTHORIZATION
      IMPORTING keys REQUEST requested_authorizations FOR post_prod RESULT result.
    METHODS upload FOR MODIFY
      IMPORTING keys FOR ACTION post_prod~upload RESULT result.

ENDCLASS.

CLASS lhc_post_prod IMPLEMENTATION.

  METHOD get_instance_authorizations.
*----------------------------------------------------------------------*
* Development ID: ZDDE-00106697                                        *
* Functional  ID: ZFSE-00106696                                        *
* Description: Method for authorizations                               *
*----------------------------------------------------------------------*
*  Change Log:                                                         *
* ---------------------------------------------------------------------*
*    Who       Date           CR            Text                       *
*  KOPPIKR2  17-Dec-2025   1100282721   Initial version                *
*----------------------------------------------------------------------*

    DATA: lv_upd TYPE abap_bool VALUE abap_false,
          lv_del TYPE abap_bool VALUE abap_false.

    CONSTANTS: lc_actvt     TYPE fieldname      VALUE 'ACTVT',
               lc_02        TYPE activ_auth     VALUE '02',
               lc_ddlname   TYPE fieldname      VALUE 'TABLE',
               lc_post_prod TYPE fieldname      VALUE 'ZOPS_POST_PROD'.
*- Reading the Entries of the entity
    READ ENTITIES OF zops_i_post_prod IN LOCAL MODE
    ENTITY post_prod
    ALL FIELDS WITH CORRESPONDING #( keys )

    RESULT DATA(lt_prod)
    FAILED DATA(lt_failed).

*- Checking if the internal table is not initial
    IF lt_prod[] IS NOT INITIAL.

*- Checking the authorization Object
      AUTHORITY-CHECK OBJECT 'S_TABU_NAM'
         ID lc_actvt  FIELD   lc_02
         ID lc_ddlname  FIELD lc_post_prod.
      IF sy-subrc <> 0.
        lv_upd = abap_true.
        lv_del = abap_true.
      ENDIF.

      LOOP AT lt_prod ASSIGNING FIELD-SYMBOL(<lfs_prod>).

        APPEND VALUE #( LET upd_auth = COND #( WHEN lv_upd = abap_true THEN if_abap_behv=>auth-unauthorized
                                                     ELSE if_abap_behv=>auth-allowed )
                            del_auth = COND #( WHEN lv_del = abap_true THEN if_abap_behv=>auth-unauthorized
                                                     ELSE if_abap_behv=>auth-allowed )
                              IN
                               %tky            = <lfs_prod>-%tky
                               %update         = upd_auth
                               %action-edit    = upd_auth
                               %delete         = del_auth
                            ) TO result.

      ENDLOOP.
    ENDIF.
  ENDMETHOD.

  METHOD upload.

*----------------------------------------------------------------------*
* Development ID: ZDDE-00106697                                        *
* Functional  ID: ZFSE-00106696                                        *
* Description: Method to upload the excel                              *
*----------------------------------------------------------------------*
*  Change Log:                                                         *
* ---------------------------------------------------------------------*
*    Who       Date           CR            Text                       *
*  KOPPIKR2  17-Dec-2025   1100282721   Initial version                *
*----------------------------------------------------------------------*

    CONSTANTS: lc_a  TYPE c VALUE 'A',
               lc_b  TYPE c VALUE 'B',
               lc_c  TYPE c VALUE 'C',
               lc_d  TYPE c VALUE 'D',
               lc_e  TYPE c VALUE 'E',
               lc_f  TYPE c VALUE 'F',
               lc_g  TYPE c VALUE 'G',
               lc_h  TYPE c VALUE 'H',
               lc_i  TYPE c VALUE 'I',
               lc_j  TYPE c VALUE 'J',
               lc_k  TYPE c VALUE 'K',
               lc_l  TYPE c VALUE 'L',
               lc_m  TYPE c VALUE 'M',
               lc_n  TYPE c VALUE 'N',
               lc_o  TYPE c VALUE 'O',
               lc_p  TYPE c VALUE 'P',
               lc_q  TYPE c VALUE 'Q',
               lc_r  TYPE c VALUE 'R',
               lc_s  TYPE c VALUE 'S',
               lc_t  TYPE c VALUE 'T',
               lc_u  TYPE c VALUE 'U',
               lc_v  TYPE c VALUE 'V',
               lc_w  TYPE c VALUE 'W',
               lc_x  TYPE c VALUE 'X',
               lc_y  TYPE c VALUE 'Y',
               lc_z  TYPE c VALUE 'Z',
               lc_aa TYPE char2 VALUE 'AA',
               lc_ab TYPE char2 VALUE 'AB'.

*Excel data will be extracted and sent as XSTRING data
    FIELD-SYMBOLS: <fs_table>   TYPE STANDARD TABLE.
    DATA: update_tab  TYPE TABLE FOR UPDATE zops_i_post_prod,
          create_tab1 TYPE TABLE FOR CREATE zops_i_post_prod,
          lr_data     TYPE REF TO data.

    DATA(ls_fileinfo) = VALUE #( keys[ 1 ]-%param OPTIONAL ).
    DATA(ls_keys) = VALUE #( keys[ 1 ] OPTIONAL ).

    IF ls_fileinfo-file_content IS NOT INITIAL.
      TRY.
          DATA(lo_xlsx) = NEW cl_fdt_xl_spreadsheet(
            document_name = CONV #( TEXT-001 )
            xdocument     = ls_fileinfo-file_content
            mime_type     = ls_fileinfo-mime_type
          ).
        CATCH cx_fdt_excel_core INTO DATA(lref_excel_core).
          APPEND VALUE #( %tky = ls_keys-%tky ) TO failed-post_prod.
          APPEND VALUE #( %tky = ls_keys-%tky
                          %msg    = new_message_with_text(
                                      severity = if_abap_behv_message=>severity-error
                                      text     = lref_excel_core->if_message~get_text( )
                                    )
                          %global = if_abap_behv=>mk-on
                        ) TO reported-post_prod.
          RETURN.
      ENDTRY.
*Get the work sheet details..
      lo_xlsx->if_fdt_doc_spreadsheet~get_worksheet_names(
        IMPORTING
          worksheet_names = DATA(lt_worksheets)
      ).
      IF lt_worksheets IS NOT INITIAL.
        ASSIGN lt_worksheets[ 1 ] TO FIELD-SYMBOL(<fs_worksheet>).
      ELSE.
        APPEND VALUE #( %tky = ls_keys-%tky ) TO failed-post_prod.
        APPEND VALUE #( %tky = ls_keys-%tky
                        %msg    = new_message_with_text(
                                    severity = if_abap_behv_message=>severity-error
                                    text     = TEXT-004
                                  )
                        %global = if_abap_behv=>mk-on
                      ) TO reported-post_prod.
        RETURN.
      ENDIF.
      DATA(lo_data) = lo_xlsx->if_fdt_doc_spreadsheet~get_itab_from_worksheet(
        worksheet_name = <fs_worksheet> ).
      IF lo_data IS BOUND.
        ASSIGN lo_data->* TO <fs_table>.
*Read excel data
        IF <fs_table> IS ASSIGNED.
          CREATE DATA lr_data LIKE LINE OF <fs_table>.
          LOOP AT <fs_table> ASSIGNING FIELD-SYMBOL(<fs_data>).
            IF sy-tabix = 1.
              CONTINUE.
            ENDIF.
            CHECK <fs_data> IS NOT INITIAL.
            APPEND INITIAL LINE TO lhc_post_prod=>mt_dbtable ASSIGNING FIELD-SYMBOL(<fs_dbtable>).
            LOOP AT CAST cl_abap_structdescr( cl_abap_typedescr=>describe_by_data_ref( lr_data ) )->get_components( )
                  REFERENCE INTO DATA(lr_components).
              ASSIGN COMPONENT lr_components->name OF STRUCTURE <fs_data> TO FIELD-SYMBOL(<fs_value>).
              IF <fs_value> IS ASSIGNED.
                CASE lr_components->name.
                  WHEN lc_a.
                    IF <fs_value> IS INITIAL.
                      APPEND VALUE #( %tky = ls_keys-%tky ) TO failed-post_prod.
                      APPEND VALUE #( %tky = ls_keys-%tky
                                       %msg    = new_message_with_text(
                                                   severity = if_abap_behv_message=>severity-error
                                                   text     = TEXT-002
                                                 )
                                       %global = if_abap_behv=>mk-on
                                     ) TO reported-post_prod.
                      EXIT.
                    ELSE.
                      <fs_dbtable>-zbatch = <fs_value>.
                    ENDIF.
                  WHEN lc_b.
                    IF <fs_value> IS INITIAL.
                      APPEND VALUE #( %tky = ls_keys-%tky ) TO failed-post_prod.
                      APPEND VALUE #( %tky = ls_keys-%tky
                                       %msg    = new_message_with_text(
                                                   severity = if_abap_behv_message=>severity-error
                                                   text     = TEXT-003
                                                 )
                                       %global = if_abap_behv=>mk-on
                                     ) TO reported-post_prod.
                      EXIT.
                    ELSE.
                      <fs_dbtable>-zvialnum = <fs_value>.
                    ENDIF.
                  WHEN lc_c.
                    <fs_dbtable>-zkdauf = <fs_value>.
                  WHEN lc_d.
                    <fs_dbtable>-zcompany = <fs_value>.
                  WHEN lc_e.
                    <fs_dbtable>-zpmbqnty = <fs_value>.
                  WHEN lc_f.
                    <fs_dbtable>-zrmbqnty = <fs_value>.
                  WHEN lc_g.
                    <fs_dbtable>-zmatnr = <fs_value>.
                  WHEN lc_h.
                    <fs_dbtable>-zitem = <fs_value>.
                  WHEN lc_i.
                    <fs_dbtable>-zplant = <fs_value>.
                  WHEN lc_j.
                    <fs_dbtable>-zphour = <fs_value>.
                  WHEN lc_k.
                    <fs_dbtable>-zthour = <fs_value>.
                  WHEN lc_l.
                    <fs_dbtable>-zamci = <fs_value>.
                  WHEN lc_m.
                    <fs_dbtable>-zinjdat = <fs_value>.
                  WHEN lc_n.
                    <fs_dbtable>-zimpstat = <fs_value>.
                  WHEN lc_o.
                    <fs_dbtable>-zvialstat = <fs_value>.
                  WHEN lc_p.
                    <fs_dbtable>-zvialqnty = <fs_value>.
                  WHEN lc_q.
                    <fs_dbtable>-zvolact = <fs_value>.
                  WHEN lc_r.
                    <fs_dbtable>-zacttc = <fs_value>.
                  WHEN lc_s.
                    <fs_dbtable>-zcaldat = <fs_value>.
                  WHEN lc_t.
                    <fs_dbtable>-zexpdattim = <fs_value>.
                  WHEN lc_u.
                    <fs_dbtable>-zshpdattim = <fs_value>.
                  WHEN lc_v.
                    <fs_dbtable>-zdosenum = <fs_value>.
                  WHEN lc_w.
                    <fs_dbtable>-ztimezone = <fs_value>.
                  WHEN lc_x.
                    <fs_dbtable>-ztrdocnum = <fs_value>.
                  WHEN lc_y.
                    <fs_dbtable>-zsalqty = <fs_value>.
                  WHEN lc_z.
                    <fs_dbtable>-zsot = <fs_value>.
                  WHEN lc_aa.
                    <fs_dbtable>-zdispstatus = <fs_value>.
                  WHEN lc_ab.
                    <fs_dbtable>-zimporteddatetime = <fs_value>.
                ENDCASE.
              ENDIF.
            ENDLOOP.

*            Prepare Internal table for Update
            update_tab = VALUE #( BASE update_tab
                         (
                         %data = CORRESPONDING #( <fs_dbtable> MAPPING TO ENTITY )
                         %control = VALUE #( zbatch  = if_abap_behv=>mk-on
                                             zvialnum  = if_abap_behv=>mk-on
                                             zkdauf  = if_abap_behv=>mk-on
                                             zcompany = if_abap_behv=>mk-on
                                             zpmbqnty = if_abap_behv=>mk-on
                                             zrmbqnty = if_abap_behv=>mk-on
                                             zmatnr = if_abap_behv=>mk-on
                                             zitem = if_abap_behv=>mk-on
                                             zplant = if_abap_behv=>mk-on
                                             zphour = if_abap_behv=>mk-on
                                             zthour = if_abap_behv=>mk-on
                                             zamci = if_abap_behv=>mk-on
                                             zinjdat = if_abap_behv=>mk-on
                                             zimpstat = if_abap_behv=>mk-on
                                             zvialstat = if_abap_behv=>mk-on
                                             zvialqnty = if_abap_behv=>mk-on
                                             zvolact = if_abap_behv=>mk-on
                                             zacttc = if_abap_behv=>mk-on
                                             zcaldat = if_abap_behv=>mk-on
                                             zexpdattim = if_abap_behv=>mk-on
                                             zshpdattim = if_abap_behv=>mk-on
                                             zdosenum = if_abap_behv=>mk-on
                                             ztimezone = if_abap_behv=>mk-on
                                             ztrdocnum = if_abap_behv=>mk-on
                                             zsalqty = if_abap_behv=>mk-on
                                             zsot =  if_abap_behv=>mk-on
                                             zdispstatus = if_abap_behv=>mk-on
                                             zimporteddatetime = if_abap_behv=>mk-on
                                )
                             ) ).
*            Prepare Internal table for Create
            create_tab1 = VALUE #( BASE create_tab1
                        (
                        %data = CORRESPONDING #( <fs_dbtable> MAPPING TO ENTITY )
                        %control = VALUE #( zbatch  = if_abap_behv=>mk-on
                                            zvialnum  = if_abap_behv=>mk-on
                                            zkdauf  = if_abap_behv=>mk-on
                                            zcompany = if_abap_behv=>mk-on
                                            zpmbqnty = if_abap_behv=>mk-on
                                            zrmbqnty = if_abap_behv=>mk-on
                                            zmatnr = if_abap_behv=>mk-on
                                            zitem = if_abap_behv=>mk-on
                                            zplant = if_abap_behv=>mk-on
                                            zphour = if_abap_behv=>mk-on
                                            zthour = if_abap_behv=>mk-on
                                            zamci = if_abap_behv=>mk-on
                                            zinjdat = if_abap_behv=>mk-on
                                            zimpstat = if_abap_behv=>mk-on
                                            zvialstat = if_abap_behv=>mk-on
                                            zvialqnty = if_abap_behv=>mk-on
                                            zvolact = if_abap_behv=>mk-on
                                            zacttc = if_abap_behv=>mk-on
                                            zcaldat = if_abap_behv=>mk-on
                                            zexpdattim = if_abap_behv=>mk-on
                                            zshpdattim = if_abap_behv=>mk-on
                                            zdosenum = if_abap_behv=>mk-on
                                            ztimezone = if_abap_behv=>mk-on
                                            ztrdocnum = if_abap_behv=>mk-on
                                            zsalqty = if_abap_behv=>mk-on
                                            zsot =  if_abap_behv=>mk-on
                                            zdispstatus = if_abap_behv=>mk-on
                                            zimporteddatetime = if_abap_behv=>mk-on
                               )
                            ) ).
          ENDLOOP.

          IF update_tab IS NOT INITIAL.
            SELECT * FROM zops_post_prod AS a
            INNER JOIN @lhc_post_prod=>mt_dbtable AS b
            ON a~zbatch = b~zbatch AND
            a~zvialnum = b~zvialnum
            INTO TABLE @DATA(lt_existing_db).
            IF lt_existing_db IS INITIAL.
* Create at table level
              MODIFY ENTITIES OF zops_i_post_prod IN LOCAL MODE
              ENTITY post_prod
              CREATE FIELDS ( zbatch zvialnum zkdauf zcompany zpmbqnty zrmbqnty zmatnr zitem zplant zphour zthour zamci zinjdat zimpstat zvialstat zvialqnty
                              zvolact zacttc zcaldat zexpdattim zshpdattim zdosenum ztimezone ztrdocnum zsalqty zsot zdispstatus zimporteddatetime )
              WITH create_tab1
              FAILED   DATA(failed_data1)
              REPORTED DATA(reported_data1)
              MAPPED   DATA(mapped_data1).
            ENDIF.
          ENDIF.
* modify at table level
          MODIFY ENTITIES OF zops_i_post_prod IN LOCAL MODE
          ENTITY post_prod
          UPDATE FIELDS ( zkdauf zcompany zpmbqnty zrmbqnty zmatnr zitem zplant zphour zthour zamci zinjdat zimpstat zvialstat zvialqnty
                          zvolact zacttc zcaldat zexpdattim zshpdattim zdosenum ztimezone ztrdocnum zsalqty zsot zdispstatus zimporteddatetime )
          WITH update_tab
          FAILED   DATA(failed_data2)
          REPORTED DATA(reported_data2)
          MAPPED   DATA(mapped_data2).
          READ ENTITIES OF zops_i_post_prod IN LOCAL MODE
          ENTITY post_prod
          ALL FIELDS WITH CORRESPONDING #( keys )
          RESULT DATA(lt_data).
          result = VALUE #( FOR ls_data IN lt_data
                          ( %tky = ls_data-%tky
                            %param = ls_data ) ).
        ELSE.
*           Error handling
          APPEND VALUE #( %tky = ls_keys-%tky ) TO failed-post_prod.
          APPEND VALUE #( %tky = ls_keys-%tky
                         %msg    = new_message_with_text(
                                     severity = if_abap_behv_message=>severity-error
                                     text     = |{ TEXT-005 }|
                                   )
                         %global = if_abap_behv=>mk-on
                       ) TO reported-post_prod.
        ENDIF.
      ENDIF.
    ENDIF.
  ENDMETHOD.

ENDCLASS.
********************************************ABSTRACT ENTITY FOR FILE UPLOAD***********************************************************
<img width="839" height="438" alt="image" src="https://github.com/user-attachments/assets/b98d523f-7509-421d-88b9-0979496a03ac" />
**************************************************DATA ELEMENT*********************************************************************
<img width="867" height="566" alt="image" src="https://github.com/user-attachments/assets/351ae9ca-eedf-4bc3-8846-00d78b67ebfe" />

