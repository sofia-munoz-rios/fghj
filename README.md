# Parcial Bioinformática
markdown
preview = ctrl + shift + m
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

https://github.com/sofia-munoz-rios/pprimer-repositorio.git

## Nombre

Nombre Apellido

## Introducción

En este trabajo se realizó el procesamiento y análisis bioinformático de los datos suministrados utilizando diferentes herramientas para el control de calidad, ensamblaje, alineamiento, filogenia y análisis estadístico. A continuación, se describe paso a paso la metodología empleada y la interpretación biológica de los resultados obtenidos.

---

# Punto 1. Control de calidad y ensamblaje

## 1.1 Control de calidad

### Código utilizado

```bash
fastqc reads_R1.fastq.gz reads_R2.fastq.gz
```

### Resultados

Se generaron los reportes de calidad para ambas librerías de secuenciación.

### Interpretación

Si la calidad es buena:

> Las secuencias presentan una buena calidad general, ya que la mayoría de las bases tienen valores Phred superiores a 30. Esto indica una baja probabilidad de error en la secuenciación y permite continuar con los análisis posteriores.

Si la calidad es regular:

> Aunque la calidad general es aceptable, se observa una disminución de los valores Phred hacia los extremos de las lecturas, por lo que es recomendable realizar un proceso de filtrado.

---

## 1.2 Filtrado de secuencias

### Código utilizado

```bash
trimmomatic PE \
reads_R1.fastq.gz reads_R2.fastq.gz \
R1_paired.fastq.gz R1_unpaired.fastq.gz \
R2_paired.fastq.gz R2_unpaired.fastq.gz \
ILLUMINACLIP:TruSeq3-PE.fa:2:30:10 \
LEADING:3 TRAILING:3 \
SLIDINGWINDOW:4:20 MINLEN:50
```

### Interpretación

> El filtrado permitió eliminar adaptadores y regiones de baja calidad, conservando únicamente las lecturas adecuadas para los análisis posteriores.

---

## 1.3 Conteo de kmers

### Código utilizado

```bash
jellyfish count -m 21 -s 100M -t 8 reads.fastq
```

### Resultados

| Métrica | Antes del filtrado | Después del filtrado |
|----------|----------|----------|
| Kmers totales | XXXX | XXXX |
| Singletons | XXXX | XXXX |

### Interpretación

Si disminuyen los singletons:

> Después del filtrado se observó una reducción importante en el número de singletons, lo que indica una disminución del ruido asociado a errores de secuenciación.

---

## 1.4 Ensamblaje de novo

### Código utilizado

```bash
spades.py \
-1 R1_paired.fastq.gz \
-2 R2_paired.fastq.gz \
-o ensamblaje
```

### Estadísticas del ensamblaje

| Métrica | Valor |
|----------|----------|
| N50 | XXXX |
| L50 | XXXX |
| Número de contigs | XXXX |
| Tamaño total ensamblado | XXXX |

### Interpretación

Si el ensamblaje es bueno:

> El ensamblaje presenta un N50 elevado y un L50 bajo, lo que sugiere una buena continuidad de los contigs y una baja fragmentación.

Si el ensamblaje es malo:

> El alto número de contigs y un N50 reducido indican que el ensamblaje se encuentra fragmentado, posiblemente debido a regiones repetitivas o limitaciones en la calidad de las lecturas.

---

# Punto 2. BLAST o análisis filogenético

## Descarga de secuencias

### Código utilizado

```bash
efetch -db nucleotide -id ACCESION -format fasta
```

---

## Renombramiento de encabezados

### Código utilizado

```bash
sed 's/patron/reemplazo/'
```

### Explicación

> Se utilizaron expresiones regulares para conservar únicamente el código de acceso y el nombre de la especie, facilitando la interpretación de los resultados posteriores.

---

## Alineamiento múltiple

### Código utilizado

```bash
muscle -in secuencias.fasta -out alineamiento.fasta
```

### Interpretación

> El alineamiento permitió identificar regiones conservadas y variables entre las secuencias analizadas.

---

## Árbol filogenético

### Código utilizado

```bash
iqtree2 -s alineamiento.fasta -bb 1000
```

### Resultado

![Árbol filogenético](arbol.png)

### Interpretación

Si la secuencia agrupa con otra especie:

> La secuencia analizada se agrupó con ________, indicando una relación evolutiva cercana. Los altos valores de bootstrap respaldan esta agrupación.

Si la secuencia aparece separada:

> La secuencia forma una rama independiente, lo que podría indicar una divergencia evolutiva importante o la ausencia de secuencias más cercanas en la base de datos analizada.

---

# Punto 3. Interpretación de BLAST

### Código utilizado

```bash
tblastn \
-query proteinas.fasta \
-db contigs \
-out resultados.txt
```

### Resultados

| Parámetro | Valor |
|------------|----------|
| Identity (%) | XXXX |
| Coverage (%) | XXXX |
| E-value | XXXX |
| Bitscore | XXXX |

### Interpretación

Si el E-value es cercano a cero:

> El valor E extremadamente bajo indica que la similitud observada difícilmente ocurrió por azar.

Si la identidad es alta:

> El porcentaje de identidad obtenido sugiere que ambas secuencias presentan una alta conservación evolutiva.

Si la cobertura es alta:

> La cobertura indica que gran parte de la secuencia consulta está representada en el alineamiento.

---

# Punto 4. Gráficas en R

## Código utilizado

```R
library(ggplot2)

ggplot(datos,
aes(x=fecha,y=casos))+
geom_line()
```

---

## Gráfica 1

![Grafica 1](grafica1.png)

### Interpretación

Si existe una tendencia creciente:

> Se observa un incremento progresivo de la variable analizada a lo largo del tiempo, lo que podría indicar una expansión o aumento de frecuencia.

Si existe una tendencia decreciente:

> Los resultados muestran una disminución progresiva de la variable analizada, sugiriendo una reducción de su incidencia o abundancia.

---

## Gráfica 2

![Grafica 2](grafica2.png)

### Interpretación

Si existe correlación positiva:

> Se observa una relación positiva entre las variables analizadas, indicando que a medida que aumenta una de ellas también aumenta la otra.

Si no existe correlación:

> No se observa una tendencia clara entre las variables, por lo que no parece existir una relación directa entre ellas.

---

---

# Interpretaciones adicionales que suelen aparecer en los parciales

## FastQC

### Si la calidad es excelente

> La mayoría de las bases presentan valores Phred superiores a 30, indicando una baja probabilidad de error y una alta confiabilidad de las lecturas.

### Si la calidad disminuye al final de las lecturas

> Se observa una reducción de la calidad hacia los extremos de las lecturas, un patrón común en tecnologías Illumina. Por esta razón es recomendable aplicar procesos de trimming antes del ensamblaje.

### Si aparecen adaptadores

> La presencia de adaptadores indica contaminación técnica derivada del proceso de secuenciación, por lo que deben eliminarse para evitar sesgos en los análisis posteriores.

---

## Kmers

### Si hay muchos singletons

> Una alta proporción de singletons suele asociarse con errores de secuenciación o regiones con muy baja cobertura.

### Si disminuyen después del filtrado

> La reducción de singletons después del trimming sugiere que gran parte de los errores presentes en las lecturas fueron eliminados exitosamente.

---

## Ensamblaje

### Si el N50 es alto

> Un N50 elevado indica que gran parte del ensamblaje está representado por contigs largos, sugiriendo una buena continuidad.

### Si el N50 es bajo

> Un N50 bajo indica que el ensamblaje está fragmentado y compuesto por numerosos contigs pequeños.

### Si el L50 es bajo

> Un valor bajo de L50 indica que pocos contigs son suficientes para representar el 50 % del ensamblaje total, lo que refleja una mejor calidad del ensamblaje.

### Si existen muchos contigs

> Un número elevado de contigs suele indicar fragmentación del ensamblaje, posiblemente causada por regiones repetitivas o cobertura insuficiente.

### Si preguntan por ensamblaje de novo

> El ensamblaje de novo permite reconstruir un genoma sin utilizar una secuencia de referencia, siendo especialmente útil para organismos cuyo genoma no ha sido previamente caracterizado.

---

## BLAST

### Si el E-value es cercano a cero

> El valor E extremadamente bajo indica que la similitud observada es altamente significativa y difícilmente se debe al azar.

### Si la identidad es superior al 90 %

> El alto porcentaje de identidad sugiere una fuerte conservación evolutiva entre las secuencias comparadas.

### Si la cobertura es alta

> La alta cobertura indica que gran parte de la secuencia consulta se encuentra representada en el alineamiento.

### Si piden interpretar el mejor hit

> El mejor alineamiento corresponde a ________, con una identidad de XX %, una cobertura de XX % y un E-value cercano a cero, lo que sugiere una relación evolutiva cercana o una posible homología funcional.

### Si preguntan por qué se utilizó tblastn

> Se utilizó tblastn porque la secuencia consulta corresponde a una proteína y se desea compararla contra una base de datos de secuencias nucleotídicas traducidas en los diferentes marcos de lectura.

---

## Filogenia

### Si el bootstrap es alto (>70)

> Los valores altos de bootstrap respaldan estadísticamente las relaciones observadas en el árbol filogenético.

### Si el bootstrap es bajo

> Los bajos valores de bootstrap indican incertidumbre en algunas relaciones evolutivas representadas en el árbol.

### Si la secuencia se agrupa con una especie

> La agrupación observada sugiere una relación evolutiva cercana y la presencia de un ancestro común relativamente reciente.

### Si la secuencia aparece aislada

> La posición aislada podría indicar una divergencia evolutiva importante o la ausencia de secuencias cercanas dentro del conjunto analizado.

---

## SARS-CoV-2

### Si la muestra agrupa con Ómicron

> La secuencia analizada presenta una mayor cercanía evolutiva con la variante Ómicron, sugiriendo que comparte varias de sus mutaciones características.

### Si la muestra agrupa con Delta

> La secuencia muestra una relación más estrecha con la variante Delta, indicando una posible pertenencia a este linaje o a uno estrechamente relacionado.

### Si aumentan simultáneamente mutaciones y casos

> Existe una asociación temporal entre el incremento de mutaciones y el aumento de casos, aunque esto no implica necesariamente una relación causal directa.

### Si aumentan las mutaciones pero no los casos

> El número de mutaciones por sí solo no parece explicar completamente la dinámica epidemiológica observada.

---

## Gráficas poblacionales

### Si existe correlación positiva

> Los resultados sugieren que ambas variables aumentan de manera conjunta, indicando una posible asociación biológica.

### Si existe correlación negativa

> A medida que una variable aumenta, la otra disminuye, lo que podría indicar una relación inversa entre ambas.

### Si no existe correlación

> No se observa una tendencia clara que permita establecer una relación directa entre las variables analizadas.

---

## Arañas y gradientes altitudinales

### Si el morfo rojo aumenta con la altitud

> El incremento del morfo rojo en zonas elevadas podría indicar adaptación local a las condiciones ambientales presentes a mayores altitudes.

### Si el morfo blanco disminuye con la altitud

> La disminución de este fenotipo podría sugerir una menor aptitud en ambientes de mayor elevación.

### Si la supervivencia aumenta con la temperatura

> Los resultados sugieren que temperaturas más altas favorecen el desempeño fisiológico o la supervivencia de los individuos.

### Si la supervivencia disminuye con la temperatura

> La reducción de la supervivencia podría indicar efectos negativos asociados al estrés térmico.

---

## Preguntas teóricas frecuentes

### ¿Por qué las proteínas no tienen intrones?

> Los intrones están presentes en el ADN y son eliminados durante el procesamiento del ARN mediante el proceso de splicing. Como consecuencia, las proteínas se traducen únicamente a partir de los exones presentes en el ARNm maduro.

### ¿Qué es un ortólogo?

> Los genes ortólogos son genes presentes en especies diferentes que se originaron a partir de un ancestro común y generalmente conservan funciones similares.

### ¿Qué es una secuencia conservada?

> Es una región que ha permanecido relativamente estable a lo largo de la evolución debido a que cumple funciones biológicas importantes.

### ¿Qué indica una alta identidad entre secuencias?

> Sugiere una relación evolutiva cercana o una fuerte conservación funcional.

### ¿Qué significa cobertura?

> La cobertura representa la cantidad de veces que una región del genoma ha sido secuenciada y constituye una medida de confianza en los resultados obtenidos.
