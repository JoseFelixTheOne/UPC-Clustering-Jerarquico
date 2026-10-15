# Plan de implementación: Ward con 30k y distribución por GitHub

**Estado:** pendiente de implementación. Este documento registra el plan; no implica que sus tareas se hayan ejecutado.

## 1. Objetivo y viabilidad

Preparar un **repositorio público con código y datos**, ejecutable en VS Code y Colab, manteniendo exactamente **30,000 registros para Ward**.

El Parquet pesa **58,616,958 bytes (~56 MiB)**: puede versionarse directamente en Git, aunque GitHub emitirá una advertencia por superar 50 MiB. Está debajo del bloqueo de 100 MiB; no necesitamos Git LFS para este archivo. [Documentación oficial](https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-large-files-on-github).

**Condición previa:** verificar fuente, permiso de redistribución y privacidad del dataset. No publicar datos ni muestras hasta superar esa revisión. Si no existe autorización, conservar el código público y documentar la descarga desde la fuente autorizada.

### Alcance

- Trabajar exclusivamente en `Clustering_Jerarquico`.
- Usar 30,000 registros directamente para Ward, sin muestra intermedia de 300k ni propagación por centroides.
- Mantener las 13 características actuales y la semilla 42.
- No integrar todavía los notebooks de K-Means y DBSCAN.
- No considerar 30k un óptimo estadístico demostrado: es el tamaño acordado para esta etapa.

## 2. Repositorio reproducible

- Incorporar el Parquet original en `data/`, sin duplicarlo ni convertirlo a CSV. Actualmente está fuera del repositorio de Clustering_Jerarquico.
- Documentar procedencia, condiciones de uso y transformación del dataset limpio.
- Registrar SHA-256 para comprobar que todos utilizan el mismo archivo.
- Usar rutas relativas, independientes del nombre del usuario o ubicación del proyecto.
- Incluir dependencias y una guía: **clonar → instalar → seleccionar kernel → ejecutar todo**.
- Excluir entornos virtuales, credenciales y archivos temporales.
- Mantener el dataset estable: modificar binarios repetidamente incrementaría el historial del repositorio.
- No incluir todavía los notebooks de los otros modelos.
- Configurar el remoto de GitHub durante la implementación; no hay uno configurado en el estado auditado.

## 3. Limpieza y optimización del notebook

### Problemas verificados

- Ward se calcula dos veces: primero con SciPy `linkage` y después con `AgglomerativeClustering`.
- Se prepara una muestra de 300k antes de seleccionar 30k y se escala sobre los 300k.
- La propagación por centroides reasigna incluso observaciones usadas para construir la jerarquía.
- Las métricas repiten trabajo y el tiempo total reportado omite etapas.
- El notebook no conserva outputs ejecutados.

### Cambios previstos

- Eliminar la preparación intermedia de 300k y seleccionar directamente **30k reproducibles**, sin reemplazo y con semilla 42. Registrar identificadores de origen y distribución anual.
- Leer únicamente las columnas utilizadas.
- Mantener las 13 características actuales; validar columnas, categorías desconocidas, faltantes y valores finitos. Detenerse con un mensaje claro si no existen 30k registros elegibles; no reducir la muestra silenciosamente.
- Evitar que categorías desconocidas se conviertan silenciosamente en categorías normales o pérdidas cero. Registrar las imputaciones realizadas.
- Ajustar el escalador sobre los 30k.
- Calcular **un único `linkage` Ward** y reutilizarlo para dendrograma y todos los cortes. Eliminar el segundo ajuste y `NearestCentroid`.
- Usar las etiquetas Ward originales en métricas, tablas y perfiles.
- Comparar k=3–6; conservar k=4 como configuración inicial, sin declararlo óptimo anticipadamente. Comprobar que la partición final tiene cuatro grupos.
- Evaluar Silhouette sobre los mismos 5k para cada corte; reutilizar resultados y calcular Davies–Bouldin sobre los 30k. Conservar métricas sin redondear en los archivos de resultados.
- Corregir el corte del dendrograma: derivar una altura compatible con la partición o retirar la línea si los empates impiden representarla fielmente.
- Medir carga, preparación, Ward, evaluación y duración total por separado.
- Retirar la supresión global de warnings y corregir o tratar de forma específica los avisos relevantes.
- Retirar comentarios editoriales sobre inviabilidad/RAM, complejidades incorrectas y conclusiones no demostradas. Conservar metodología y limitaciones pertinentes.
- Generar las conclusiones descriptivas desde los resultados reales, sin anticipar eficacia clínica.

### Interpretación metodológica

Presentar los resultados como **segmentación retrospectiva de perfiles maternos y del parto**, no como predicción prenatal: algunas características describen el parto ya ocurrido.

Los desenlaces clínicos se utilizarán para caracterizar los grupos, no como características de entrada. Registrar la distribución anual; no introducir una partición temporal ni cambiar las variables silenciosamente durante esta optimización. La validación temporal y la comparación entre modelos se abordarán en la integración posterior.

## 4. Ejecución y persistencia

Agregar una sección inicial con rutas de entrada/salida y parámetros visibles: tamaño de muestra 30k, semilla 42, k final inicial 4, candidatos 3–6 y evaluación de Silhouette de 5k.

### VS Code

- Clonar el repositorio obtiene también la data, una vez autorizada su distribución.
- Detectar el Parquet desde la estructura del repositorio o aceptar una ruta configurada.
- Documentar creación del entorno, instalación de dependencias y selección del kernel.
- Ejecutar desde un kernel limpio, sin intervención entre celdas.

### Colab

- Abrir el notebook desde GitHub y clonar el repositorio en el entorno para disponer del Parquet. No asumir que abrir el notebook descarga los demás archivos.
- Montar Google Drive con autorización y configurar una carpeta persistente de resultados.
- No exigir montaje de Drive ni interacción de Colab al ejecutar localmente.
- Documentar una única preparación inicial antes de ejecutar todas las celdas.
- Referencia: [funcionamiento de Colab](https://research.google.com/colaboratory/faq.html).

### Resultados que se guardarán

- Gráficos PNG: dendrograma, perfiles estandarizados y tasas clínicas por cluster.
- Tabla comparativa de k, perfiles de características y perfiles clínicos.
- Etiquetas de los 30k con identificador de origen.
- Manifiesto con configuración, versiones, identificadores de muestra y tiempos.
- Notebook ejecutado y exportación HTML mediante un comando reproducible, sin intentar que el notebook se ejecute a sí mismo.

Mantener gráficos y tablas visibles dentro del notebook. Documentar también el guardado después de “Ejecutar todo”: generar archivos externos y guardar los outputs del notebook son mecanismos distintos.

Actualizar dependencias, incluyendo las herramientas necesarias para ejecutar y exportar notebooks, y registrar las versiones efectivas. No modificar APIs externas ni los otros modelos.

## 5. Validación y aceptación

- [ ] Revisar permiso y privacidad **antes de cualquier publicación**, incluidos outputs del notebook.
- [ ] Probar una clonación limpia y verificar la integridad del dataset.
- [ ] Ejecutar una prueba pequeña del flujo antes de la corrida completa.
- [ ] Ejecutar los 30k desde un kernel limpio, sin errores ni dependencia de celdas ejecutadas previamente.
- [ ] Confirmar exactamente 30k etiquetas y cuatro grupos finales.
- [ ] Verificar que tamaños y perfiles sumen 30k, que los outputs estén presentes y que las exportaciones sean legibles y no vacías.
- [ ] Repetir para comprobar selección y partición reproducibles, sin depender del número arbitrario asignado a cada cluster.
- [ ] Validar realmente VS Code y Colab; registrar cualquier plataforma no probada como pendiente.
- [ ] Medir duración completa: **diez minutos sigue siendo un objetivo, no una garantía**.
- [ ] Integrar los tres notebooks únicamente después de cerrar esta etapa.

## Próximo paso

Implementar este plan solo después de una autorización posterior. La creación de este documento no autoriza ejecutar notebooks, instalar dependencias, crear repositorios remotos ni publicar código o datos.

## Aprendizajes clave

1. La optimización principal es eliminar el segundo Ward y la evaluación repetida, no reducir características arbitrariamente.
2. El Parquet actual cabe en GitHub sin Git LFS; la autorización para redistribuirlo todavía debe verificarse.
3. Clonar resuelve la distribución del código y la data, pero no sustituye la instalación de dependencias ni el guardado persistente de resultados.
