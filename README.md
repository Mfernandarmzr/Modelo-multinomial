# Modelo-multinomial
Base de datos de Latinobarómetro en Excel (variables: año, país, edad, sexo, autoubicación de la ideología).

#1. Guardar y cargar la base en R

#archivo está en Excel, entonces se necesita readxl:

# Instalar paquetes 
install.packages("readxl")
install.packages("nnet")
install.packages("marginaleffects")
install.packages("dplyr")

# Cargar librerías
library(readxl)
library(nnet)
library(marginaleffects)
library(dplyr)

# Cargar archivo Excel (ajusta la ruta a donde lo tengas guardado)
data <- read_excel("ideologia.xlsx")

# Ver estructura
head(data)
str(data)


#2. Revisar variables

#De tu archivo las variables son:
	#•	Year = Año (numérica, no nos importa ahora).
	#•	Pais = País (numérica, puede servir para agrupar).
	#•	Edad = Edad (numérica).
	#•	Sexo = Sexo (1 = hombre, 2 = mujer).
	#•	P16ST = Autoubicación en escala izquierda-derecha (0–10, donde a veces se codifica 97 = No sabe).

#Antes de usar, hay que limpiar datos raros como el “97”:

# Reemplazar 97 en ideología por NA
data <- data %>%
  mutate(
    P16ST = ifelse(P16ST == 97, NA, P16ST),
    Sexo = factor(Sexo, levels = c(1,2), labels = c("Hombre", "Mujer"))
  )

# Quitar filas con NA en ideología
data <- na.omit(data)


#3. Convertir la variable dependiente en categórica

#En modelos multinomiales se necesita que la variable dependiente sea factor con varias categorías (no solo números).
#Tu escala de 0–10 se puede tratar como categórica:

# Variable dependiente: ideología
data$P16ST <- factor(data$P16ST)

table(data$P16ST) 
# ver distribución



#4. Ajustar el modelo multinomial

#Ejemplo: ideología según edad y sexo.

# Ajustar modelo multinomial
modelo <- multinom(P16ST ~ Edad + Sexo, data = data, trace = FALSE)

# Resultados
summary(modelo)

# Odds ratios
exp(coef(modelo))


5. Efectos marginales

Para interpretar más fácil:

ame_edad <- avg_slopes(modelo, variables = "Edad", type = "probs")
ame_edad

ame_sexo <- avg_slopes(modelo, variables = "Sexo", type = "probs")
ame_sexo

