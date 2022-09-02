![GitHub all releases](https://img.shields.io/github/downloads/leninkelvin/ADFR-of-varying-flexibility/total?style=for-the-badge&logo=appveyor)
![GitHub forks](https://img.shields.io/github/forks/leninkelvin/ADFR-of-varying-flexibility?style=for-the-badge&logo=appveyor)
![GitHub Repo stars](https://img.shields.io/github/stars/leninkelvin/ADFR-of-varying-flexibility?style=for-the-badge&logo=appveyor)

# ADFR, docking con flexibilidad de cadenas laterales.
Estos archivos conforman las instrucciones básicas para realizar docking con ADFR con un receptor rígido. Además se agregan instrucciones para realizar dockings con cadenas laterales flexibles. 
Se usará ADFR para el docking, se puede descargar [aquí](https://ccsb.scripps.edu/adfr/).

El receptor es la proteína Human Estrogen Receptor Alpha [1L2I](https://www.rcsb.org/structure/1L2I). 

Su ligando es la molécula [ETC](https://www.rcsb.org/ligand/ETC). 

Las cadenas laterales flexibles se seleccionan usando la interfaz gráfica agfrgui usando radios de 3, 3.5, 4 y 5 angstroms de distancia respecto al ligando de referencia, ETC. 

# Introdución

# Material
### Advertencia: estas instrucciones son generales y simplificadas, no representan el mejor flujo de trabajo ni el úico posible. Sin embargo, deben funcionar cuando se siguen. 

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

Los archivos del RCSB no son perfectos, y algunas imperfecciones son sufientes como para causar errores en el procedimiento de docking. Para corregir automaticamente algunos de estos errores, usaremos [UCSF Chimera](https://www.cgl.ucsf.edu/chimera/). Usando Chimera, abriremos el PDB que creamos arriba, 1L2Ir.pdb, y lo procesaremos con la herramienta *Energy Minimization* . Ah, pero primero selecciona la cadena A y luego invierte la selección parar borrar todas las cadenas excepto la A. 

![select](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/select.png)

![invert](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/invert.png)

![delete](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/delete.png)

Después de estos pasos, procedemos a la minimización.

![minimize](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/minimize.png)

Estos setting son los que yo recomiendo pero pueden ser modificados.

![basic](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/basic.png)

Estos settings también son los que recomiendo. Es importante borrar las moléculas de agua, en el caso de nuestro ejemplo ya borramos los ligandos.

![dockprep](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/dockprep.png)

![hydrogens](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/hydrogens.png)

Ahora, a salvar el archivo:

![edited](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/edited.png)

Listo ahora a convertir el ligando al formato PDBQT: 
```
prepare_ligand -l ETC.pdb -o ETC.pdbqt -A bonds_hydrogens
```
Con esta instrucción se agregan los hidrógenos. Esto podría no ser necesario si los ligandos provienen de una base de datos particular. Los ligandos obtenidos del RCSB si requieren esa adición.

```
prepare_receptor -r 1L2Ire.pdb -o 1L2Ir.pdbqt -A bonds_hydrogens -U nphs
```
Con esta instrucción se prepara el receptor, también se les agregan los hidrógenos y se colapsan los hidrógenos no polares. El PDB 1L2I tiene residuos en conformaciones alternas. 

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

### Nota del uso de CPU: muchos CPUs tiene hyperthreads. Es un método de planificación de tareas que los sistemas usualmente ven como más núcleos. Para procesos como docking, no ayudan. Para restringuir el uso de CPU de manera eficiente hay que usar el -c n, donde n es el número de cores que deseas usar.

# Resultados

```
mode |  affinity  | clust. | ref. | clust. | rmsd | energy | best |
     | (kcal/mol) | rmsd   | rmsd |  size  | stdv |  stdv  | run  |
-----+------------+--------+------+--------+------+--------+------+
   1        -11.0     0.0     0.7     100     0.2     0.1    041
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


# Preparar archivos para docking con residuos flexibles.

1. El archivo 1L2Ir.pdbqt que hemos preparado arriba volverá a ser usado con el comando:

```
agfrgui
```
Este comando, cuando se ejecuta en una computadora MacOS (con Xquartz) o linux (con X11), desplegará una interfaz gráfica. 

![AGFRGUI](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/AGFRGUI.png)

2. Ahora hay que localizar el 1L2Ir.pdbqt que ya habiamos preparado

![Receptor](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/Receptor.png)

3. Y, para facilitar el proceso, usaremos el ligando ETC.pdbqt que hemos usado como control positivo arriba. Daremos click en el botón de [ligand] PDBQT...

![LigandReference](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/LigandReference.png)

4. Ahora, podemos dar click al boton que restringe la caja de docking a nuestro ligando de referencia (enmarcado con un hexágo), luego extender el padding a 8 angstroms (enmarcado por u elipse). Ahora, seleccionar el boton que nos permite indicar una distancia para los residuos laterales (en este caso, el rectangulo).

![Flexible](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/Flexible.png)

5. Hay que calcular los pockets (elipse).

![Pockets](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/Pockets.png)

6. Si se van a realizar dockings con una variedad de móleculas se debe seleccionar "for all atom types". Si este paso no se realiza, las rejillas de docking solo funcionarán para los mismo átomos que estan representados en el ligando de referencia.

![Atoms](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/Atoms.png)

7. Finalmente se generan el "target file" y se salva el archivo.

![Save](https://github.com/leninkelvin/ADFR-of-varying-flexibility/blob/main/Save.png)

8. Ahora podemos ejecutar el comando
```
adfr -l ETC.pdbqt -t 1L2Ir3.trg -J PosCosR -c 4 -n 100 -e 2500000 -r ETC.pdbqt -T
```
Hay que tener la precaución de seleccionar el "target file" que generamos con residuos flexibles. 

# Resultados, docking flexible

### Control positivo, ETC.

```
mode |  affinity  | clust. | ref. | clust. | rmsd | energy | best |
     | (kcal/mol) | rmsd   | rmsd |  size  | stdv |  stdv  | run  |
-----+------------+--------+------+--------+------+--------+------+
   1        -12.0     0.0     0.8     100     0.3     0.2    042
```

### Control negativo, GOL.

```
adfr -l GOL.pdbqt -t 1L2Ir3.trg -J PosCosR -c 4 -n 100 -e 2500000 -T
```

```
mode |  affinity  | clust. | ref. | clust. | rmsd | energy | best |
     | (kcal/mol) | rmsd   | rmsd |  size  | stdv |  stdv  | run  |
-----+------------+--------+------+--------+------+--------+------+
   1         -4.3     0.0    -1.0      44     0.5     0.1    080
   2         -4.0     2.5    -1.0       2     0.1     0.0    019
   3         -4.0     8.1    -1.0      52     0.3     0.1    039
   4         -3.9     6.9    -1.0       2     0.1     0.0    066
```
Ninguno de los dos resultados es mucho mejor que el rigido. Claro, son controles postivos y negativos, respectivamente. Pero, ahora ya saben como hacer sus propias pruebas.

# Referencias

[ADFR](https://ccsb.scripps.edu/adfr/)

# Créditos

Author [Lenin Domínguez-Ramírez](https://github.com/leninkelvin)
