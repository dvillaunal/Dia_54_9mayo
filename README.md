```{r}
"Protocolo:

+ Daniel Felipe Villa Rengifo

+ Lenguaje: R

+ Tema: Manejo de archivos *.csv (realice al menos dos ejercicios que requieran cargar archivos externos, leer y procesar la información del archvo leído, y guardar las respuestas a los ejercicios  en archivos independientes)

+ Fuentes:

  1. https://www.generatedata.com
   
  2. https://r-coder.com/leer-csv-r/"
```


# ¿Cómo leer un CSV en R?

Para cargar los datos utilizamos la función `read.csv(…)`, esta es la función más sencilla para leer archivos CSV.

```{r Ejemplo,eval=FALSE}
# Por defecto coma (,) como separador y punto (.) como separador decimal
read.csv(file,                 # Nombre del archivo o ruta completa del archivo
         header = TRUE,        # Leer el encabezado (TRUE) o no (FALSE)
         sep = ",",            # Separador de los valores
         quote = "\"",         # Caracter de citaciones
         dec = ".",            # Punto decimal
         fill = TRUE,          # Rellenar celdas vacías (TRUE) o no (FALSE)
         comment.char = "",    # Carácter de los comentarios o cadenas vacías
         encoding = "unknown", # Codificación del archivo
         ...)                  # Argumentos adicionales

# Por defecto punto y coma (;) como separador y coma (,) como separador decimal:
read.csv2(file, header = TRUE, sep = ";", quote = "\"", dec = ",",
          fill = TRUE, comment.char = "", encoding = "unknown", ...)
```


# 1° Ejemplo

El primer ejercicio consiste en importar una base de datos de una central de envios donde contienen los siguientes parametros:

+ `Region`: Continente

+ `item type`: Productos que envia:
   
   1. Office Supplies
   2. Vegetables
   3. Fruits
   4. Cosmetics
   5. Cereal
   6. Baby Food
   7. Beverages
   8. Snacks
   9. Clothes
   10. Household
   11. Personal Care
   12. Meat

+ `Sales Channel`: Canal de la venta:
   
   1. Online
   2. Offline
   
+ `Order Priority`:Prioridad del envio
   
   1. `C`: Critico
   2. `H`: Alto
   3. `M`: Medio
   4. `L`: Bajo

+ `Unit Price`: Precio unitario

+ `Unit Cost`: Costo Unitario

+ `Total Revenue`: Ingresos totales

+ `Total Cost`: Costos totales

+ `Total Profit`: Beneficio Total

## Importar la base de datos:

```{r}
# Importar la base de datos:
## Vamos a convertir las columnas de la 1 a la 4 en factores:

"Gracias a esto cuando hagamos el resumen, se generaran niveles en los cuales haran un conteo de cada uno, necesario para el ejercicio"

base_envios <- read.csv("Central de Envios.csv", stringsAsFactors = T)
```

ahora vamos a hacer un resumen el cual nos dara unos datos importantes:

```{r}
# Hacer un resumen de la base de datos anterior:
resumen = summary(base_envios)

## Ahora exportamos el resumen como un archivo csv:
write.csv(resumen, file = "Resumen.csv")
```

## Tabla Ingresos totales, respecto al canal de venta:

Primero tenemos que filtrar nuestra base de datos extrayendo solo los siguientes datos:

+ `Region`: Continente

+ `Sales Channel`: Canal de la venta:
   
   1. Online
   2. Offline
   
+ `Total Revenue`: Ingresos totales


```{r}
## Importamos la libreria dplyr:
library(dplyr)

# Creamos un dataframe con las anteriores columnas:
ingresoXCanal <- select(base_envios, Region, Sales.Channel, Total.Revenue)

## Exportemos el resultado, eliminando la numeración:
write.csv(ingresoXCanal, file = "IngresoXCanal.csv", row.names = F)
```

```{r}
# Separemos los datos para hacer un conteo por canal de venta:
online <- filter(ingresoXCanal, Sales.Channel == "Online")

offline <- filter(ingresoXCanal, Sales.Channel == "Offline")

# Intentemos hacer una tabla de datos para online y offline (conteo) para hallar quien vendio mas o menos por Región:

solucion_online <- aggregate(Total.Revenue ~ Region, online, sum)

solucion_offline <- aggregate(Total.Revenue ~ Region, offline, sum)
```

Ahora ya podemos exportar y ver quien tuvo más o mneos ingresos

```{r}
# Exportamos los datos anteriores:
write.csv(solucion_online, file = "Solucion_Online.csv", row.names = F)

write.csv(solucion_offline, file = "Solucion_Offline.csv", row.names = F)
```




## hagamos una suma en la cual veamos los beneficios totales por prioridad de orden:

```{r}
# Hagamos una selección de las columnas solicitadas:

beneficioXprioridad <- select(base_envios, Order.Priority, Total.Profit)

# ahaora hagamos una suma por prioridades:
resultado <- aggregate(Total.Profit ~ Order.Priority, beneficioXprioridad, sum)
# Visualicemos:
print(resultado)

# Exportemos el resultado a csv:
write.csv(resultado, file = "Resultado.csv", row.names = F)
```

# 2° Ejercicio:

```{r}
#Importemos la base de datos:
base <- read.csv("Ejemplo.csv")
```

Vamos a realizar el siguiente ejercicio:

+ Vamos  a filtrar la base de datos para ver si es:
   
   1. Si es mayor de edad (+18)
   2. si es parte de la tercera edad (+60)  
   
   
+ Vamos a hacer un conteo por sus colores favoritos, para descubrir que color es mas favorecido.


```{r}
# Empecemos por filtrar edades:

# Veamos si es mayor de edad, para serlo (en colombia) la mayoria de edad se cumple a los 18:

mas18 <- filter(base, Ages >= 18)
print(mas18)

# Extraigamos todas las personas que son mayores de edad:

mas60 <- filter(base, Ages >= 60)
print(mas60)

# Exportemos los resultados:
write.csv(mas18, file = "Mayor_Edad.csv", row.names = F)

write.csv(mas60, file = "Terccera_Edad.csv", row.names = F)

```

Ahora vamos hacer un conteo para descubrir cual es el color mas favorecido:

```{r}
#Generemos un conteo con la función table():

likeCol <- data.frame(table(base$favorite.color))

print("Observamos que el color ganador es el verde con una diferencia de 1 contra el amarillo")

# Exportemos el resultado:
write.csv(likeCol, file = "Conteo_ColFavorite.csv", row.names = F)
```


