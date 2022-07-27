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

# Métodos




## The second largest heading
###### The smallest heading
