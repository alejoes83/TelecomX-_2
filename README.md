
# TelecomX-_2
el tercer challenge para alura ONE
en este Clallenge se va a desarrollar modelos predictivos capaces de prever qué clientes tienen mayor probabilidad de cancelar sus servicios.

La empresa TelecomX quiere anticiparse al problema de la cancelación, y vamos a construir un pipeline robusto para esta etapa inicial de modelado.

🧠 **Objetivos del Desafío**

1. Preparar los datos para el modelado (tratamiento, codificación, normalización).

2. Realizar análisis de correlación y selección de variables.

3. Entrenar dos o más modelos de clasificación.

4. Evaluar el rendimiento de los modelos con métricas.

5. Interpretar los resultados, incluyendo la importancia de las variables.

6. Crear una conclusión estratégica señalando los principales factores que influyen en la cancelación.





**Informe de cancelación de clientes – TelecomX**
**1) Resumen**

Tasa de churn: 25.7% (clientes que cancelaron) vs 74.3% que permanecen.

Mejor modelo: XGBoost con scale_pos_weight (AUC ≈ 0.81). Superó a:

XGBoost + SMOTE (AUC ≈ 0.76)

Regresión Logística (Accuracy 0.80; mejor F1 en Churn que Random Forest)

Random Forest (Accuracy 0.79)

Driver visual confirmado: clientes con cargos mensuales más altos muestran mayor probabilidad de churn (boxplot de Charges.Monthly vs Churn).

Patrones consistentes (según correlaciones ≥0.07, gráficos y modelos):

Contrato: Month-to-month ↑ churn; contratos anuales/bianuales ↓ churn.

Tenure (antigüedad): baja antigüedad ↑ churn.

Cargos mensuales: altos ↑ churn.

Método de pago: Electronic check ↑ churn; pagos automáticos ↓.

Servicios de soporte/seguridad: ausencia de OnlineSecurity y/o TechSupport ↑ churn.

PaperlessBilling: suele asociarse a ↑ churn (probable efecto de perfil/canal, no causal).

Conclusión operativa: Adoptar XGBoost + ajuste de umbral como clasificador principal y focalizar acciones en los segmentos con cargos altos, tenure bajo y contrato mes a mes, especialmente si usan pago electronico y no tienen seguridad en linea o soporte técnico.

**2) Datos y metodología**

Variables: tras One-Hot Encoding y filtrado (|corr| ≥ 0.07) se trabajó con un conjunto reducido de predictores.

Preparación de los datos:

Eliminados customerID y otros derivados redundantes.

Estandarización para modelos sensibles a escala (Regresión Logística).

Balanceo:

Probado SMOTE (solo en train) y alternativa sin SMOTE usando scale_pos_weight en XGBoost.

Evaluación: train/test split estratificado; métricas por clase (precision, recall, F1), matriz de confusión, AUC-ROC.

**3) Rendimiento de los modelos**

Regresión Logística: Accuracy 0.80; F1 (Churn) 0.57; mejor recall de Churn que RF.

Random Forest: Accuracy 0.79; F1 (Churn) 0.53; buen desempeño en No Churn, pierde Churn.

XGBoost + SMOTE: AUC 0.76; mejora algo el recall, menor generalización.

XGBoost + scale_pos_weight: AUC 0.81; mejor discriminación global y buen equilibrio precision/recall en Churn.

Matriz de confusión (RF): alto TN, pero muchos FN (churn no detectado). El ajuste de clase en XGBoost ayuda a reducir ese problema.

**4) Principales factores que afectan la cancelación**

Integrando correlaciones, gráficos (boxplots, tasas por categoría) y señales coincidentes en importancias de modelos:

Cargos mensuales (Charges.Monthly) ↑

Clientes con cobros altos concentran más churn.

Hipótesis: sensibilidad al precio/valor percibido.

Tenure (antigüedad) ↓

Churn se concentra en clientes nuevos / de baja antigüedad.

Ventana crítica: primeros meses (“onboarding”).

Tipo de contrato (Contract)

Month-to-month ↑ churn.

One/Two year ↓ churn (compromiso + descuentos).

Método de pago (PaymentMethod)

Electronic check ↑ churn.

Débito/Crédito automático ↓ churn (menor fricción y olvidos).

Servicios de seguridad/soporte (OnlineSecurity, TechSupport)

Ausencia de estos servicios ↑ churn.

Posible relación con percepción de soporte/calidad/valor.

InternetService

Ciertas combinaciones (por ejemplo, Fiber optic sin seguridad/soporte) muestran más churn (posible tensión precio/expectativa/calidad).

Nota: Los signos ↑/↓ indican dirección de asociación con mayor probabilidad de churn, no causalidad estricta.

**5) Estrategias de retención basadas en los hallazgos**
**A.** Sensibilidad al precio (Charges.Monthly ↑)

implementar paquetes con descuento para cargos altos (ej. agregar OnlineSecurity/TechSupport con bundle price).

Ofertas escalonadas: descuento temporal 2–3 meses para clientes con riesgo alto.

Revisión de precio por elasticidad: micro-segmentar por uso/valor percibido.

**B.** Tenure bajo (onboarding)

Programa de bienvenida (0–90 días):

Setup guiado, tips de uso, chequeos proactivos de calidad.

Check-ins por WhatsApp/Email en días 7, 30 y 60.

Garantía de satisfacción/upgrade sin costo el primer mes.

**C.** Contratos mes a mes

Migración a anual con incentivos: 1–2 meses bonificados o upgrade de velocidad/servicio.

Pop-ups/in-app nudges: resaltar ahorro anual estimado.

**D.** PaymentMethod = Electronic check

Campaña de cambio a débito/Crédito (1 click):

- Incentivo pequeño (5% por 2 meses).

- Mensajes como: “evita cortes/recargos, paga automáticamente”.

**E.** Falta de seguridad/soporte (OnlineSecurity/TechSupport = No)

Cross-sell de OnlineSecurity/TechSupport dirigido a clientes en riesgo (especialmente de Fiber optic).

Pruébalo gratis 1 mes + tutorial de valor.

**F.** Service-quality en Fiber optic

- Encuestas y monitoreo de incidencias por zona.

- Cuadrillas proactivas donde se detecte mayor churn.

**G.** Orquestación por propensión (con el modelo)

Umbral de activación ajustado a objetivo (recall vs precisión).

Playbooks por segmento de riesgo:

- Riesgo alto: llamada + oferta fuerte.

- Riesgo medio: email/SMS + oferta moderada.

- Riesgo bajo: educación de producto.

**6) Operativización y próximos pasos**

Modelo en producción: XGBoost scale_pos_weight, recalibrado mensualmente.

Umbral optimizado por costo: elegir cut-off que maximice beneficio esperado (coste de incentivo vs valor del cliente salvado).

Explicabilidad: usar SHAP para explicar drivers por cliente.

A/B testing: medir uplift real de cada acción (por segmento/canal).

Calidad de datos: monitorear data drift; revisar outliers en Charges.Total y Monthly.

Nuevas variables (si están disponibles): tickets/averías recientes, uso/consumo, velocidad contratada vs entregada, histórico de pagos.

**7) Qué entregar a negocio**

Lista semanal de clientes en riesgo con score y “razones” (precio alto, tenure bajo, método de pago, etc.).

Panel con KPIs: tasa de contacto, conversión de oferta, churn salvado.
