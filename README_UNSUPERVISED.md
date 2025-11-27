# Componente no supervisado con K-Means (EA3)

## 1. Descripcion de la tecnica
- **K-Means** agrupa observaciones en K centros minimizando la distancia euclidiana dentro de cada cluster.
- Es simple, escalable y facilita interpretar perfiles de clientes en riesgo de default.
- El ajuste se realiza **solo con el set de entrenamiento (`data/train.csv`)** para evitar data leakage. La etiqueta `default` no participa en el entrenamiento no supervisado; se usa despues para evaluar los segmentos.

## 2. Datos utilizados
- Variables numericas ejemplo: `age`, `income`, `loan_amount`, `loan_income_ratio`, `num_late_payments`, `credit_score` (si alguna no existe se seleccionan automaticamente las numericas disponibles).
- `default` solo se usa para revisar la tasa de incumplimiento por cluster y entender el riesgo relativo de cada grupo.

## 3. Procedimiento resumido
1. Seleccion de variables numericas relevantes (se excluye `default`).
2. Imputacion simple de nulos con la media del entrenamiento.
3. Estandarizacion con `StandardScaler` para que todas las variables aporten por igual a la distancia.
4. Metodo del codo probando K en [2, 8] para elegir un K razonable (se parte con K=3).
5. Entrenamiento de `KMeans` y asignacion de la etiqueta `cluster` a cada cliente.
6. Analisis de promedios por cluster y calculo de la tasa de default por cluster.

## 4. Resultados e interpretacion (corrida de referencia)
- Con el K=3 sugerido, los perfiles observados fueron coherentes con riesgo crediticio:
  - **Cluster 0**: relacion deuda/ingreso alta y mas moras; tasa de default ~0.125.
  - **Cluster 1**: ingresos medios y morosidad moderada; tasa de default ~0.048.
  - **Cluster 2**: ingresos altos y pocas moras; tasa de default ~0.003.
- Estos valores provienen de la corrida de referencia del notebook; si se usa un dataset distinto, volver a ejecutar para obtener las tasas propias.
- Uso sugerido para negocio: segmentar campanas, definir politicas de riesgo diferenciadas, priorizar monitoreo de los clusters con mayor tasa de default.

## 5. Conclusiones y mejoras
- Se recomienda evaluar incorporar la etiqueta `cluster` como feature adicional en el modelo supervisado, validando impacto en metricas antes de pasar a produccion.
- Posibles mejoras: probar puntaje silhouette para seleccionar K, tecnicas alternativas como DBSCAN o clustering jerarquico, y reentrenar K-Means periodicamente para capturar cambios en el portafolio.
