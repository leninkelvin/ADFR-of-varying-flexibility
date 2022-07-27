# ADFR, docking con flexibilidad de cadenas laterales.
Estos archivos conforman las instrucciones básicas para realizar docking con ADFR con un receptor rígido. Además se agregan instrucciones para realizar dockings con cadenas laterales flexibles. 
Se usará ADFR para el docking, se puede descargar [aquí](https://ccsb.scripps.edu/adfr/).

El receptor es la proteína Human Estrogen Receptor Alpha [1L2I](https://www.rcsb.org/structure/1L2I). 

Su ligando es la molécula [ETC](https://www.rcsb.org/ligand/ETC). 

Las cadenas laterales flexibles se seleccionan usando la interfaz gráfica agfrgui usando radios de 3, 3.5, 4 y 5 angstroms de distancia respecto al ligando de referencia, ETC. 

# Introdución

# Material

Se usará ADFR para el docking, se puede descargar [aquí](https://ccsb.scripps.edu/adfr/). Nosotros instalamos **ADFR** en /opt/ADFR de manera que:
```
export PATH=/opt/ADFR/bin/:$PATH
```
El receptor es la proteína Human Estrogen Receptor Alpha [1L2I](https://www.rcsb.org/structure/1L2I). Con el código de abajo se descarga el archivo y se extrae la proteína sin heteroátomos.
```
wget http://files.rcsb.org/download/1L2I.pdb
grep ATOM 1L2I.pdb > 1L2Ir.pdb
```
Su ligando es la molécula [ETC](https://www.rcsb.org/ligand/ETC). Con este comando se extrae el ligando. 
```
grep "ETC A" 1L2I.pdb > ETC.pdb
```

Como controles negativos usaremos [sulfato](https://drive.google.com/file/d/15TDwcHqx1EcOieZzvgfsxFjJmA4Gz1AG/view?usp=sharing) y [glicerol](https://drive.google.com/file/d/15SyLmHBO6KzTgzOEEv294eXVcIyajEHP/view?usp=sharing). Estás moléculas se seleccionaron pues son cosolutos usados en la crsitalográfia de rayos X y, aunque se observen unidos a proteínas, se espera que su interacción sea inespecífica.


# Métodos

# Resultados

# Referencias

# Créditos

First author [Valeria Montiel Carreon](https://github.com/valemontcar)

Corresponding author [Lenin Domínguez-Ramírez](https://github.com/leninkelvin)
