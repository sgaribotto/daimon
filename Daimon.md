### **Nombre Clave del Proyecto: "Daimon" (o Daimon: Your Critical Thinking Tutor)**

### **1\. Stack Tecnológico Propuesto**

Dado que te sientes cómodo con Python/Flask/Postgres, ese stack es **ideal**. No hace falta cambiarlo, pero le agregaremos "superpoderes":

* **Backend:** Python con **Flask**.  
* **Base de Datos Relacional:** **PostgreSQL**.  
* **Base de Datos Vectorial (El "Cerebro"):** Aquí está el truco. Podemos usar la extensión `pgvector` en tu mismo Postgres (para mantener todo en un solo lugar) o una librería ligera como **ChromaDB** o **FAISS** que corre localmente en Python. Esto es necesario para la búsqueda semántica.  
* **Frontend:** HTML/CSS/JS simple con **Tailwind** o **Bootstrap** (renderizado por Jinja2 de Flask). Al ser una "web app de escritorio", correrá en `localhost` en tu navegador.  
* **LLM Engine:** API de Gemini (o similar) para el procesamiento.

### **2\. Funcionalidades Principales (MVP)**

#### **Módulo A: Ingesta y Procesamiento ("The Cruncher")**

* **Input:** Archivos PDF (libros, papers, apuntes).  
* **Proceso:**  
  1. Extracción de texto limpio del PDF.  
  2. **Segmentación Semántica:** El LLM analiza el texto y lo divide no por páginas, sino por temas (ej: "Capítulo 1: Introducción" \-\> "Chunk 1.1: Definiciones").  
  3. **Metadatos:** A cada chunk se le asigna:  
     * Título descriptivo.  
     * Tiempo de lectura estimado (basado en conteo de palabras / velocidad promedio).  
     * Vector de embeddings (para que el tutor sepa de qué habla).

#### **Módulo B: Planificación ("The Strategist")**

* **Roadmap Lógico:** El sistema analiza los títulos de los chunks y genera un árbol de dependencias.  
* **Calculadora de Tiempo:**  
  * *Input Usuario:* "Tengo 5 horas semanales".  
  * *Cálculo:* (Total Horas Contenido \+ Factor de Repaso) / 5 \= Fecha estimada de finalización.  
* **Dashboard:** Muestra "Próxima acción" (Leer X, Repasar Y, Evaluar Z).

#### **Módulo C: Estudio y Evaluación ("The Dojo")**

* **Modo Lectura:** Visualización limpia del texto del chunk.  
* **Generador de Evaluaciones (On-demand):**  
  * *Flashcards:* Generadas automáticamente del chunk (Concepto \<-\> Definición).  
  * *Multiple Choice:* Para verificaciones rápidas.  
  * *Pregunta de Desarrollo:* El usuario escribe un párrafo. El LLM lo evalúa comparándolo con el texto original y da feedback (ej: "Bien, pero olvidaste mencionar la crisis económica como causa").  
* **Algoritmo de Repaso:** Implementación simple tipo Leitner o SM-2 (si fallas, lo ves mañana; si aciertas, en 3 días).

#### **Módulo D: Tutor Socrático ("The Mentor")**

* **Chat Contextual:**  
  * **Toggle "Modo Estricto":** El prompt del sistema incluye instrucción: "Responde SOLO usando el contexto provisto. Si no está en el texto, di que no sabes".  
  * **Toggle "Modo Libre":** El prompt cambia a: "Usa el contexto como base, pero amplía con tu conocimiento general para explicar mejor".

### **specificaciones Técnicas Resumidas ("Daimon" MVP)**

* **Plataforma:** Aplicación Web Local (Escritorio).  
* **Stack:** Python \+ Flask \+ PostgreSQL (con `pgvector`) \+ HTML/Tailwind.  
* **Motor de IA:** Google Gemini API.  
* **Almacenamiento:**  
  1. *Archivos:* Sistema de archivos local (`/uploads`).  
  2. *Datos:* PostgreSQL (Metadatos, Texto de chunks, Embeddings vectoriales).  
* **Funcionalidades Clave:**  
  1. **Ingesta:** Carga de PDF \-\> OCR/Extracción \-\> División Semántica (Chunking) \-\> Generación de Embeddings.  
  2. **Roadmap:** Ordenamiento lógico de chunks y cálculo de fechas basado en disponibilidad horaria del usuario.  
  3. **Estudio:** Visor de texto limpio \+ Link al PDF original.  
  4. **Evaluación:** Flashcards \+ Multiple Choice \+ Desarrollo (Corrección IA: Nota 1-10 \+ Feedback).  
  5. **Tutor:** Chatbot RAG (Retrieval-Augmented Generation) con selector de contexto (Estricto/Libre).

---

### **Plan de Trabajo (Roadmap de Desarrollo)**

Lo dividí en 5 fases para que puedas ir probando funcionalidades sin esperar a tener todo terminado.

#### **Fase 1: Los Cimientos y la Base de Datos**

* **Objetivo:** Tener el entorno corriendo y la estructura de datos lista.  
* **Tareas:**  
  1. Configurar entorno virtual Python y repositorio.  
  2. Instalar y configurar PostgreSQL con la extensión `pgvector`.  
  3. **Diseñar el Schema SQL:** Definir tablas (`Users`, `Materials`, `Chunks`, `StudySessions`, `Reviews`).  
  4. Crear la conexión Flask-Postgres (SQLAlchemy).

#### **Fase 2: El Procesador de Conocimiento (Ingesta)**

* **Objetivo:** Que puedas subir un PDF y verlo "despezado" semánticamente en la base de datos.  
* **Tareas:**  
  1. Implementar subida de archivos y guardado en disco.  
  2. Script de extracción de texto (usando librerías como `PyPDF2` o `pdfminer`).  
  3. **Ingeniería de Prompt (Gemini):** Diseñar el prompt que toma el texto crudo y devuelve un JSON con los chunks semánticos.  
  4. Generación de Embeddings (vectorización de los chunks) y guardado en DB.

#### **Fase 3: La Interfaz de Estudio y el Tutor**

* **Objetivo:** Poder leer el contenido y chatear con él.  
* **Tareas:**  
  1. Vista de lectura (Frontend simple).  
  2. Implementar la búsqueda vectorial (buscar chunks relevantes según la pregunta del usuario).  
  3. Construir el endpoint del Chatbot con el "Toggle" (Estricto/Libre) para inyectar o no el contexto recuperado.

#### **Fase 4: El Examinador (Evaluaciones)**

* **Objetivo:** Generar preguntas y corregirlas.  
* **Tareas:**  
  1. Prompt para generar preguntas (Flashcards/MC/Desarrollo) a partir de un Chunk específico.  
  2. Interfaz para que el usuario responda.  
  3. Lógica de corrección: Enviar respuesta usuario \+ respuesta correcta ideal a Gemini y parsear la devolución (Nota \+ Feedback).

#### **Fase 5: El Organizador (Agenda y Roadmap)**

* **Objetivo:** Dar orden temporal al caos.  
* **Tareas:**  
  1. Algoritmo simple de proyección: `(Chunks restantes * Tiempo promedio) / Horas semanales`.  
  2. Vista de Dashboard: "¿Qué me toca hoy?".  
  3. Implementación básica de Repaso Espaciado (marcar un chunk para "revisar en X días").

