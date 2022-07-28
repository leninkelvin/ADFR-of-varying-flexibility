# ADFR, docking con flexibilidad de cadenas laterales.
Estos archivos conforman las instrucciones básicas para realizar docking con ADFR con un receptor rígido. Además se agregan instrucciones para realizar dockings con cadenas laterales flexibles. 
Se usará ADFR para el docking, se puede descargar [aquí](https://ccsb.scripps.edu/adfr/).

El receptor es la proteína Human Estrogen Receptor Alpha [1L2I](https://www.rcsb.org/structure/1L2I). 

Su ligando es la molécula [ETC](https://www.rcsb.org/ligand/ETC). 

Las cadenas laterales flexibles se seleccionan usando la interfaz gráfica agfrgui usando radios de 3, 3.5, 4 y 5 angstroms de distancia respecto al ligando de referencia, ETC. 

# Introdución

# Material
### Advertencia: estas instricciones son generale y simplificadas, no representan el mejor flujo de trabajo ni el úico posible. 

Se usará ADFR para el docking, se puede descargar [aquí](https://ccsb.scripps.edu/adfr/). Nosotros instalamos **ADFR** en /opt/ADFR de manera que:
```
export PATH=/opt/ADFR/bin/:$PATH
```
El receptor es la proteína Human Estrogen Receptor Alpha [1L2I](https://www.rcsb.org/structure/1L2I). Con el código de abajo se descarga el archivo y se extrae la proteína sin heteroátomos.
```
wget http://files.rcsb.org/download/1L2I.pdb
grep ATOM 1L2I.pdb > 1L2Ir.pdb
```
En este paso se genera un dimero; esto no es ideal pero, dado que tenemos las coordenadas del sitio de unión 
Su ligando es la molécula [ETC](https://www.rcsb.org/ligand/ETC). Con este comando se extrae el ligando. 
```
grep "ETC A" 1L2I.pdb > ETC.pdb
```

Como controles negativos usaremos sulfato y glicerol, esos estan incluidos aquí. Estás moléculas se seleccionaron pues son cosolutos usados en la crsitalográfia de rayos X y, aunque se observen unidos a proteínas, se espera que su interacción sea inespecífica.

# Métodos

### Preparación de los archivos

Primero, cualquier tipo de molécula al formato PDBQT: 
```
prepare_ligand -l ETC.pdb -o ETC.pdbqt -A bonds_hydrogens
```
Con esta instrucción se agregan los hidrógenos. Esto podría no ser necesario si los ligandos provienen de una base de datos particular. Los ligandos obtenidos del RCSB si requieren esa adición.

```
prepare_receptor -r 1L2Ir.pdb -o 1L2Ir.pdbqt -A bonds_hydrogens -U nphs
```
Con esta instrucción se prepara el receptor, también se les agregan los hidrógenos y se colapsan los hidrógenos no polares. El PDB 1L2I tiene residuos en conformaciones alternas. Hay que hacer un poco de procesamiento antes de preparar el archivo.
Con un editor de texto hay que encontrar el residuo 363 en el 1L2Ir.pdbqt . Esta sección se verá así:
```
ATOM    527  NH1AARG A 363      29.043  -5.901  -9.388  0.50 30.05    -0.235 N
ATOM    528  HH1A1ARG A 363      28.133  -6.304  -9.611  1.00  0.00     0.174 HD
ATOM    529  HH1A2ARG A 363      29.484  -5.248 -10.036  1.00  0.00     0.174 HD
ATOM    530  NH2AARG A 363      30.833  -5.709  -7.967  0.50 28.49    -0.235 N
ATOM    531  HH2A1ARG A 363      31.301  -5.964  -7.097  1.00  0.00     0.174 HD
ATOM    532  HH2A2ARG A 363      31.274  -5.056  -8.615  1.00  0.00     0.174 HD
ATOM    533  N   VAL A 364      26.412  -8.560  -1.859  1.00 39.91    -0.346 N
```
Hay que editar esa sección para que se vea así:
```
ATOM    527  NH1 ARG A 363      29.043  -5.901  -9.388  0.50 30.05    -0.235 N
ATOM    528  HH1 ARG A 363      28.133  -6.304  -9.611  1.00  0.00     0.174 HD
ATOM    529  HH1 ARG A 363      29.484  -5.248 -10.036  1.00  0.00     0.174 HD
ATOM    530  NH2 ARG A 363      30.833  -5.709  -7.967  0.50 28.49    -0.235 N
ATOM    531  HH2 ARG A 363      31.301  -5.964  -7.097  1.00  0.00     0.174 HD
ATOM    532  HH2 ARG A 363      31.274  -5.056  -8.615  1.00  0.00     0.174 HD
ATOM    533  N   VAL A 364      26.412  -8.560  -1.859  1.00 39.91    -0.346 N
```
O, pueden copiar esta sección reemplazando la del archivo original.
Hay que repetir este procedimiento con las líneas:
```
ATOM   3827  NZ ALYS B 492      25.504   6.756  35.651  0.50 42.62    -0.079 N 
ATOM   3828  HZ A1LYS B 492      25.407   7.738  35.394  1.00  0.00     0.274 HD
ATOM   3829  HZ A2LYS B 492      25.646   6.642  36.655  1.00  0.00     0.274 HD
ATOM   3830  HZ A3LYS B 492      24.620   6.254  35.562  1.00  0.00     0.274 HD
ATOM   3831  N   ALA B 493      28.617   0.862  31.925  1.00 36.38    -0.346 N 
```
para que se vea así:
```
ATOM   3827  NZ  LYS B 492      25.504   6.756  35.651  0.50 42.62    -0.079 N 
ATOM   3828  HZ  LYS B 492      25.407   7.738  35.394  1.00  0.00     0.274 HD
ATOM   3829  HZ  LYS B 492      25.646   6.642  36.655  1.00  0.00     0.274 HD
ATOM   3830  HZ  LYS B 492      24.620   6.254  35.562  1.00  0.00     0.274 HD
ATOM   3831  N   ALA B 493      28.617   0.862  31.925  1.00 36.38    -0.346 N 
```
Y, finalmente:
```
ATOM   4231  ND2AASN B 532     -17.821  -8.039  15.517  0.50 63.29    -0.370 N 
ATOM   4232  HD2A1ASN B 532     -18.059  -7.092  15.222  1.00  0.00     0.159 HD
ATOM   4233  HD2A2ASN B 532     -16.883  -8.266  15.847  1.00  0.00     0.159 HD
ATOM   4234  N   VAL B 533     -15.457 -12.076  17.250  1.00 64.95    -0.346 N 
```
Que se vea así:
```
ATOM   4231  ND2 ASN B 532     -17.821  -8.039  15.517  0.50 63.29    -0.370 N 
ATOM   4232  HD21ASN B 532     -18.059  -7.092  15.222  1.00  0.00     0.159 HD
ATOM   4233  HD22ASN B 532     -16.883  -8.266  15.847  1.00  0.00     0.159 HD
ATOM   4234  N   VAL B 533     -15.457 -12.076  17.250  1.00 64.95    -0.346 N 
```

### Preparación del receptor para el docking
Con estos dos archivos podemos proceder a la preparación del archivo trg del receptor para el docking rígido (0). 
```
agfr -r 1L2Ir.pdbqt -l ETC.pdbqt -o 1L2Ir
```
Este comando genera el trg que define la cavidad donde se hará el docking:
```
identifying pockets using AutoSite ....
    found 2 pocket(s)

    pocket|  energy | # of |Rad. of | energy |   bns    | score
    number|         |points|gyration|per vol.|buriedness|v*b^2/rg
    ------+---------+------+--------+--------+----------+---------
        1   -134.12   264    3.82     -0.51      1.00      68.82
        2    -15.54    18    1.40     -0.86      0.93      11.10
    merging clusters ...
done. got 282 fill Points, in 0.32 (sec)
```
Esta información se puede recuperar en cualquier momento usando el comando:
```
about 1L2Ir.trg
```
**Nota: este trg se puede usar para docking de cualquier ligando siempre y cuando la diana corresponda con nuestro ligando de referencia, ETC.**

### Docking

En este paso usaremos este comando:
```
adfr -l ETC.pdbqt -t 1L2Ir.trg -J PosCos -c 4 -n 100 -e 2500000 -r ETC.pdbqt -T
```
Brevemente la opciones son como sigue:

-l el ligando sobre el que se hará el docking

-t el archivo del receptor en formato trg

-J el nombre que se le asignara al resultado

-c número de cores que se usaran 

-n número de evoluciones de docking

-e número de evaluaciones de energía para el scoring de los resultados

-r ligando que se usará de referencia para calcular el RMSD del resultado

# Resultados

```
mode |  affinity  | clust. | ref. | clust. | rmsd | energy | best |
     | (kcal/mol) | rmsd   | rmsd |  size  | stdv |  stdv  | run  |
-----+------------+--------+------+--------+------+--------+------+
   1        -11.5     0.0     0.7     100     0.2     0.1    045
Writing poses to ETC_PosCos_out.pdbqt
```
### ¿Cómo se comparan los resultados de los controles?

En este punto el usuario debe repetir los pasos anteriores con el sulfato y el glicerol. 

GOL:
```
mode |  affinity  | clust. | ref. | clust. | rmsd | energy | best |
     | (kcal/mol) | rmsd   | rmsd |  size  | stdv |  stdv  | run  |
-----+------------+--------+------+--------+------+--------+------+
   1         -3.6     0.0     5.9     100     0.2     0.0    005
```
SO4:
```
mode |  affinity  | clust. | ref. | clust. | rmsd | energy | best |
     | (kcal/mol) | rmsd   | rmsd |  size  | stdv |  stdv  | run  |
-----+------------+--------+------+--------+------+--------+------+
   1         -2.1     0.0     7.3     100     0.3     0.0    025
```
Los resultados son faciles de analizar pues:
Los 100 dockings (cluster size) coinciden (clust. rmsd) y las afinidades son dramáticamente distintas.

# Referencias

# Créditos

First author [Valeria Montiel Carreon](https://github.com/valemontcar)

Corresponding author [Lenin Domínguez-Ramírez](https://github.com/leninkelvin)
