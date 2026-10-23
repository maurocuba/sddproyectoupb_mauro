# Constitución de habits-cli

1. **Stack mínimo.** Solo Python 3.12+ y biblioteca estándar; pytest únicamente para tests. Ninguna dependencia nueva.
2. **La spec manda.** Todo cambio de comportamiento o del formato JSON se escribe primero en `specs/`; el código nunca contradice la spec activa.
3. **Lógica separada de interfaz.** `core.py` es puro: sin I/O, sin `print`/`input` y sin leer el reloj (la fecha entra por parámetro). `cli.py` solo parsea argumentos y muestra resultados.
4. **Tests obligatorios.** Toda función pública de `core.py` tiene tests, todo bug corregido añade un test que lo reproduce y `pytest -q` pasa antes de cada commit.
5. **Persistencia simple.** Un único archivo JSON local, leído y escrito solo desde `storage.py`; el formato no cambia sin actualizar antes la spec.
6. **Idioma.** Identificadores, comentarios y docstrings en inglés; mensajes al usuario en español.
