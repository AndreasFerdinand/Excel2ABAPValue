# Excel2ABAPValue
Convert Excel Sheets to ABAP Value-Statements

## Motivation
tbd.

### Default Conversion
#### Source Data
| VBELN | POSNR | MATNR |
|-------|-------|-------|
| 1000  | 80    | 2543  |
| 1000  |       | 5555  |
| 1000  | 100   | 1010  |

#### Generated ABAP Code

```ABAP
DATA(LIPS) = VALUE #(
  ( VBELN = '1000' POSNR = '80' MATNR = '2543' )
  ( VBELN = '1000' POSNR = '' MATNR = '5555' )
  ( VBELN = '1000' POSNR = '100' MATNR = '1010' )
);
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
DATA(LIPS) = VALUE #(
  ( VBELN = '0000001000' POSNR = '0080' MATNR = '000000000000002543' )
  ( VBELN = '0000001000' MATNR = '000000000000005555' )
  ( VBELN = '0000001000' POSNR = '0100' MATNR = '000000000000001010' )
);
```

## Usage
### Sheet to Export
* Create a sheet. The sheet name will be used for the name of the internal table
* Use the first line of the sheet to add the name of the attributes of the internal table
* All other lines can be used to maintain values

### Optional Configuration
* Create new sheet named `#CONFIG`
* Add the following columns
  * `table`
  * `attribute`
  * `ignore`
  * `ignore_if_empty`
  * `alpha`
