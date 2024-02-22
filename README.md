# Excel2ABAPValue
Convert Excel Sheets to ABAP Value-Statements

> [!IMPORTANT]  
> Repository is under heavy development!

## Motivation
In order to simplify unit tests in SAP ABAP, this script enables you to maintain test data in Microsoft Excel and export it to ABAP `VALUE` statements, which can be used in ABAP SQL Test Double Framework.

### Default Conversion
#### Source Data
| VBELN | POSNR | MATNR |
|-------|-------|-------|
| 1000  | 80    | 2543  |
| 1000  |       | 5555  |
| 1000  | 100   | 1010  |

#### Generated ABAP Code

```ABAP
DATA(LIPS_MOCK_DATA) = VALUE #(
  ( VBELN = '1000' POSNR = '80' MATNR = '2543' )
  ( VBELN = '1000' POSNR = '' MATNR = '5555' )
  ( VBELN = '1000' POSNR = '100' MATNR = '1010' )
).
```

### Enhanced Conversion
#### Source Data
| VBELN | POSNR | MATNR |
|-------|-------|-------|
| 1000  | 80    | 2543  |
| 1000  |       | 5555  |
| 1000  | 100   | 1010  |

#### Configuration Data
| table | attribute | ignore | ignore_if_empty | alpha |
|-------|-----------|--------|-----------------|-------|
| LIPS  | VBELN     |        | X               | 10    |
| LIPS  | POSNR     |        | X               | 4     |
| LIPS  | MATNR     |        | x               | 18    |

#### Generated ABAP Code

```ABAP
DATA(LIPS_MOCK_DATA) = VALUE #(
  ( VBELN = '0000001000' POSNR = '0080' MATNR = '000000000000002543' )
  ( VBELN = '0000001000' MATNR = '000000000000005555' )
  ( VBELN = '0000001000' POSNR = '0100' MATNR = '000000000000001010' )
).
```

## Usage
### Sheet to Export
* Create a sheet. The sheet name will be used for the name of the internal table.
* Use the first line of the sheet to add the name of the attributes of the internal table.
* All other lines can be used to maintain values.

### Optional Configuration
* Create new sheet named `#CONFIG`
* Add the following columns
  * `table` - references the name of the sheet
  * `attribute` - table attribute
  * `ignore` - if set to `x` the attribute with its value won't be exported to the `VALUE` statement. Can be used for comments.
  * `ignore_if_empty` - if set to `x` **and** if the value (cell) is empty, he attribute with its value won't be exported to the `VALUE` statement.
  * `alpha` - adds leding zeroes to to fit the donoted length.


> [!CAUTION]  
> Material numbers (`MATNR`) shouldn't be converted with the simple `ALPHA` conversion as this could lead to wrong results (e.g. field length extension S/4 HANA). Use alpha method of this script with caution.

### Converting Data
* Click `Automate` and `New Script` in Ribbon bar.
* Copy & Paste the script from [test](Excel2ABAPValue.osts) to the code editor
* Click `Run`

## ABAP Unit Test

```abap
CLASS ltc_test DEFINITION FINAL FOR TESTING
  DURATION SHORT
  RISK LEVEL HARMLESS.

  PRIVATE SECTION.

    CLASS-DATA environment TYPE REF TO if_osql_test_environment.

    CLASS-METHODS class_setup.
    CLASS-METHODS class_teardown.

METHODS test_method FOR TESTING.

ENDCLASS.

CLASS ltc_test IMPLEMENTATION.
  METHOD class_setup.

    lips_mock_data TYPE STANDARD TABLE OF lips WITH EMPTY KEY.

    "create test environment and hand over all tables which should be mocked
    environment = cl_osql_test_environment=>create( VALUE #( ( 'LIPS' ) ) ).

    """"""""""""""""""""""

    "section where you have to copy the test data generated from excel by the script from this repository
    DATA(lips_mock_data) = VALUE #(
      ( VBELN = '0000001000' POSNR = '0080' MATNR = '000000000000002543' )
      ( VBELN = '0000001000' MATNR = '000000000000005555' )
      ( VBELN = '0000001000' POSNR = '0100' MATNR = '000000000000001010' )
    ).

    """"""""""""""""""""""

    "activate test data
    environment->insert_test_data( lips_mock_data ).

  ENDMETHOD.

  METHOD class_teardown.
    environment->destroy( ).
  ENDMETHOD.

  METHOD test_method.

    "Implement your test case here
    cl_abap_unit_assert=>fail( ).

  ENDMETHOD.

ENDCLASS.
```

## FAQ
### Which ABAP Version is required to use ABAP SQL Test Double Framework
As far as I know SAP NetWeaver AS for ABAP 7.52 is required.

## Further reading / related topics
* [Office Scripts documentation](https://github.com/OfficeDev/office-scripts-docs)
* [Mockup Loader for ABAP unit testing](https://github.com/sbcgua/mockup_loader)
* [Managing Database Dependencies with ABAP Unit ](https://help.sap.com/docs/ABAP_PLATFORM/c238d694b825421f940829321ffa326a/8562b437073d4b9c93078c45f7a64f21.html?locale=en-US)

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Contributing
You are welcome to send feedback, improvements, pull requests or bug reports.
