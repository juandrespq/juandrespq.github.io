---
layout: post
title: "🌡️ Usando la API de MeteoChile en Jupyter Notebooks: datos climáticos para refrigeración y ciencia de datos"
date: 2025-08-27
categories: [Python, Jupyter, APIs, Refrigeración]
tags: [Python, JupyterNotebooks, MeteoChile, Climatización, Refrigeración, CienciaDeDatos, WebServices, APIs]
---

Hola nuevamente comunidad 👋,  

En esta ocasión les traigo un ejercicio sencillo pero muy útil para quienes quieran **probar sus habilidades en Jupyter Notebooks y consumo de Web Services**.  

La idea es conectarnos con la **API REST de la Dirección Meteorológica de Chile (MeteoChile)** y obtener datos históricos de temperatura, que luego podemos analizar o graficar directamente en Python.  

---

## 🔑 Paso 1: Obtener una API Key
Para comenzar, deben registrarse en el portal de climatología:  
👉 [https://climatologia.meteochile.gob.cl/](https://climatologia.meteochile.gob.cl/)  

Allí recibirán un **usuario y token** que usaremos en nuestras consultas.  
Más detalles sobre la documentación del servicio en:  
👉 [Documentación oficial de la API](https://climatologia.meteochile.gob.cl/application/documentacion/getDocumento/17)  

---

## 🐍 Paso 2: Consultar la API con Python
Un ejemplo de cómo traer datos de **temperatura diaria histórica**:

```python
import requests
import pandas as pd

# Parámetros de la solicitud
usuario = "MiCorreo"
token = "MiToken"
estacion_id = "330020"  # ID de la estación
anio = "2024"

url = f"https://climatologia.meteochile.gob.cl/application/servicios/getTemperaturaHistoricaDiaria/{estacion_id}/{anio}?usuario={usuario}&token={token}"

response = requests.get(url)

if response.status_code == 200:
    data = response.json()
    if data["hayDatos"]:
        registros = []
        for mes, dias in data["datos"].items():
            for dia, valores in dias.items():
                registros.append({
                    "Fecha": valores["momento"],
                    "Temperatura Media": valores.get("media", None),
                    "Temperatura Máxima": valores.get("maxima", None),
                    "Fecha Máx": valores.get("fechaMax", None),
                    "Temperatura Mínima": valores.get("minima", None),
                    "Fecha Mín": valores.get("fechaMin", None),
                    "Número de Datos": valores.get("numDatos", None)
                })
        df = pd.DataFrame(registros)
    else:
        print("No hay datos para el año solicitado.")
else:
    print(f"Error en la solicitud: {response.status_code}")
```
⚠️ Importante: no olviden reemplazar **MiCorreo** y **MiToken** con sus credenciales.

---

## 📊 Paso 3: Visualizar temperaturas en un mes específico

Con los datos en un DataFrame de Pandas, podemos graficar con Matplotlib:

```python
import matplotlib.pyplot as plt

mes_deseado = 12  # diciembre

df["Fecha"] = pd.to_datetime(df["Fecha"], format="%d-%m-%Y")
df["Mes"] = df["Fecha"].dt.month
df_mes = df[df["Mes"] == mes_deseado]

plt.figure(figsize=(10, 5))
plt.plot(df_mes["Fecha"], df_mes["Temperatura Máxima"], marker="o", linestyle="-", color="red", label="Máxima")
plt.plot(df_mes["Fecha"], df_mes["Temperatura Media"], marker="s", linestyle="--", color="blue", label="Media")
plt.plot(df_mes["Fecha"], df_mes["Temperatura Mínima"], marker="^", linestyle=":", color="green", label="Mínima")

plt.xlabel("Día del Mes")
plt.ylabel("Temperatura (°C)")
plt.title(f"Temperaturas en {data['datosEstacion']['nombreEstacion']} - Mes {mes_deseado}/{anio}")
plt.legend()
plt.grid(True)
plt.show()
```

![Temperaturas en Qunta Normal diciembre de 2024 ](/assets/images/posts/TemperaturasDic2024QuintaNormal.png)


El resultado será un gráfico claro con temperaturas máximas, mínimas y medias para el mes elegido ✅.

---

## 🧩 ¿Por qué esto es útil?

Aquí es donde uno conecta informática y refrigeración:

En refrigeración y climatización, la temperatura y humedad externas afectan directamente la carga térmica de los equipos.

Con estos datos, se pueden anticipar condiciones críticas, planificar mantenimientos y hasta hacer modelos predictivos.

Para quienes estudian ciencia de datos, este es un ejercicio práctico de integración API → Pandas → Matplotlib.

## 🚀 Conclusión

Este ejemplo muestra cómo datos meteorológicos abiertos pueden tener un uso real en ingeniería aplicada y ciencia de datos.

👉 Si quieren profundizar, subí el notebook completo a mi GitHub (ahí podrán probarlo con sus propias credenciales).
👉 Y si trabajan en refrigeración o climatización, los animo a explorar cómo usar estas fuentes de datos para mejorar proyectos y procesos.

Saludos,
Juan Pavez

---

## 🔖 Hashtags

#Python #APIs #JupyterNotebooks #CienciaDeDatos #Refrigeración #Climatización #Meteorología #MeteoChile #IngenieríaAplicada #DataScience