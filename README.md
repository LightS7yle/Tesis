# Información
Se ha implementado cuatro diferentes maneras de utilizar la implementación Phi-4 mini + RAG en esta tesis, con el objetivo de comparar estas cuatro propuestas y elegir la mejor

1.- Implementación original LLM ( Phi-4 mini ) sin RAG

2.- Implementación de LLM ( Phi-4 mini ) con RAG

3.- Implementación de LLM ( Phi-4 mini ) con RAG utilizando Document Refinement with Sentence-Level Re-ranking and Reconstruction to Enhance Retrieval-Augmented Generation (DSLR)

4.- Implementación de LLM ( Phi-4 mini ) con RAG utilizando Document Refinement with Sentence-Level Re-ranking and Reconstruction to Enhance Retrieval-Augmented Generation (DSLR) utilizando Ropmura
---

## 📊 Dataset
- **Fuente**: Empresa Privada de Generación de Energía Eléctrica 
- **Registros**: 10 manuales  
- **Variables**: páginas, idioma, formato  
- **Versión usada**: descargada el 20/09/2025  

---

## 🗂️ Estructura del repositorio
```
Tesis/
  ├──  .env
  ├──  .gitignore
  ├──  README.md
  ├──  requirements.txt
  ├──  temp.txt    
dataQA/
  ├──  qa.csv                                               # Archivo preguntas/respuestas SH / respuestas modelos
  ├──  qa.txt                                               # Archivo preguntas/respuestas obtenido con el apoyo de los stakeholders (SH)
ManualesDummy/
  ├──  7.5 RO FOULING substance (anaysis solution).pdf
  ├──  Manual de Turbina TG-1 Kallpa.pdf
  ├──  MANUAL Y USO DE BOMBAS OBL SERIE R.pdf
  ├──  PD-0100-0001_Rev_m.pdf
  ├──  SB4-14-0055-GT-EN-01.pdf
  ├──  SB4-17-0022-GT-EN-01.pdf
  ├──  SB4-18-0104-GT-EN-01.pdf
  ├──  SB4-19-0009-GT-EN-01.pdf
  ├──  SB5-08-0021-GT-EN-01.pdf
  ├──  sd31_manual.pdf
notebooks/
  ├──  analisis.ipynb                                        # Análisis exploratorio inicial
  ├──  evaluacion.ipynb                                      # Evaluación de resultados
  ├──  LLM.ipynb                                             # Libro principal LLM / LLM + RAG / LLM + RAG + DSLR   
  ├──  LLM_gemma.ipynb                                       # Libro principal LLM / LLM + RAG / LLM + RAG + DSLR usando modelo Embedding Gemma     
  ├──  LLM_gemma_ropmura.ipynb                               # Libro principal LLM / LLM + RAG / LLM + RAG + DSLR usando modelo Embedding Gemma con el método ropmura
  ├──  ropmura.ipynb                                         # Cración de los agentos ropmura
outputs/                                                     
  ├──  chunks_para_qa.csv                                    # Chunks de todos los manuales
  ├──  embeddings_y_metadatos.pkl
  ├──  faiss_index.index
  ├──  embeddings_gemma_y_metadatos.pkl                      # Sentence embedding generado por Gemma     
  ├──  df_qa.csv                                             # En este .csv se guardan las respuestas generadas por mi modelo para métricas 
resultados/                                                  # Resultados comparando mis 3 alternativas: LLM / LLM + RAG / LLM + RAG + DSLR
  ├──  evaluacion_con_bert.csv                           
  ├──  evaluacion_con_rouge.csv
  ├──  evaluacion_rag_con_bert.csv
  ├──  evaluacion_rag_con_franq.csv
  ├──  evaluacion_rag_con_rouge.csv
  ├──  evaluacion_rag_dslr_con_bert.csv
  ├──  evaluacion_rag_dslr_con_rouge.csv
  ├──  Comparativa_resultados_v2.xlsx     
\
```

---

# Configuración

### Notebooks

Todas las implementaciones en notebooks son en su mayoría autoexplicativas y se encuentran comentadas.

Consta de 6 secciones:

1. Instalación y configuración de paquetes (requirements.txt)
```bash
pip install -r requirements.txt
```

2. Bloque de código extracción de data de manuales (crear en el directorio de trabajo la carpeta Manuales y agregar los limites de inicio y fin por Manual agregado)

3. Bloque de código de limpieza y preprocesamiento de datos / Generación de embeddings

4. Bloque principal de código con documentación Phi-4 mini / Phi-4 mini + RAG / Phi-4 mini + RAG + DSLR

5. Ejemplo de inferencia con 4 manuales operativos

6. Métricas de evaluación ROUGE / BERTSCORE / FRANQ

Nota-1 : Por temas de privacidad y confidencialidad de la información de la empresa no se ha subido al repositorio los manuales operativos en PDF 

Nota-2 : En caso querer probar el código del notebook "analisis.ipynb" solicitar por interno los manuales

Nota-3 : No habrá problema en probar el código descrito en el punto 4 ("LLM.ipynb") ya que los embeddings y metadatos de los manuales ya fueron extraidos

Nota-4 : La data QA se obtuvo a partir de los chunks generados en "chunks_para_qa.csv" usando un LLM como GPT-4

# Correr Notebooks

Primero hacer las configuraciones descritas anteriormente

Segundo ejecutar analisis.ipynb para obtener los embedding de tus manuales

Tercero ejecutar LLM.ipynb para guardar las inferencias generadas por los diferentes modelos descritos anteriormente.

Cuarto ejecutar evaluacion.ipynb para generar los cuadros comparativos entre los diferentes modelos.

---

## 📈 Resultados esperados (Semana 3)
- **EDA inicial** en `notebooks/analisis.ipynb`.  
- **Baseline Dummy** (LLM Phi-4 mini) → Bertscore F1 ≈ 0.828 pero sin RAG.  
- **Métrica central**: Bertscore ≈ 0.852 utilizando Phi-4 mini + RAG + DSLR
- **Logs de resultados** → `/resultados/Comparativa_resultados.xlsx`.  
- **Slides de resultados** → generados con `evaluacion.ipynb`  
---


# Trabajo futuro

Se buscará aplicar otros métodos para generación de embeddings diferente al usado ("google/embeddinggemma-300m") como lo es el Node2Vec, ganando conectar información entre manuales por su estructura técnica y relacional generando un grafo de conocimiento

Es decir, cuando un operario realiza una consulta:

Búsqueda semántica (texto): Se recupera chunks que se parecen en el texto a la consulta (FAISS)

Búsqueda estructural (grafo con Node2Vec): Se recupera manuales/nodos que están relacionados en la red con los resultados de la busqueda semántica

Ejemplo: Preguntan “procedimiento de calibración de válvula”

El texto trae el Manual A (válvulas).

Pero gracias al grafo, también se trae el Manual B (bombas), porque está estructuralmente vinculado en el sistema (comparten el mismo proveedor de válvulas/bombas)

Posibilidad de combinación de resultados texto + grafos:

Concatenación embeddings (texto + grafo)

Es decir, el LLM ya no responde solo lo más cercano a la consulta vectorialmente, sino con “lo más parecido en palabras y en relaciones técnicas”.

# 👥 Autor
Diego Fernando López Lozano
