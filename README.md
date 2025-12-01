### File System
app/: Donde vive toda la lógica.

app/models/: Aquí irán tus tablas de Postgres (SQLAlchemy).

app/routes/: Tus endpoints de Flask (Web y API).

app/services/: Aquí pondremos la lógica de Gemini (Ingesta, Chat, Evaluación).

uploads/: Donde caerán los PDFs.

run.py: El punto de entrada para arrancar el servidor.

config.py: Configuración de la base de datos y Gemini.

instance/: Donde vive la base de datos.

uploads/: Donde caerán los PDFs.
