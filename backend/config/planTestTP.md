


**Plan**
1. Mover la validación desde el MIME reportado por el cliente a una comprobación del contenido real del archivo antes de guardarlo.
2. Hacer que el rechazo devuelva un error de cliente claro, no un 500, para que el frontend pueda distinguir “archivo inválido” de “fallo interno”.
3. Ajustar el flujo de subida para que un archivo falso no llegue a persistirse ni deje basura en `uploads/`.
4. Actualizar el test simulado en SMTests.js para que espere rechazo cuando el contenido no sea un WAV válido.
5. Verificar que un WAV auténtico siga subiendo correctamente y que el error llegue al cliente como JSON estable.

Dejé el plan persistido para seguir con la implementación cuando quieras. Si prefieres, el siguiente paso puede ser uno de estos dos enfoques:

1. Validación mínima y local para WAV, sin agregar dependencias.
2. Validación robusta con detección de firma real del archivo y respuesta de error explícita.

seleccionamos la validacion robusta.








PLAN PREVIO:
Voy a revisar la configuración actual de subida y el flujo de error para ubicar el punto exacto donde validar la firma real del archivo, no solo la extensión. Después te dejo un plan concreto de cambio y cómo responder con error desde el servidor.

Ya ubiqué el punto débil: hoy la validación se hace solo por `mimetype`, así que un `.wav` renombrado puede pasar si el cliente miente. Voy a revisar si ya existe un patrón de manejo de errores de Multer y si hay tests o utilidades que convenga reutilizar.

Revisar subida de archivos y manejo de errores


La evidencia confirma que hoy no hay validación de contenido real. Ahora estoy contrastando dos opciones viables para el plan: validar la firma del archivo en el backend antes de persistirlo, o complementar con una validación específica de cabecera WAV para no depender solo del MIME reportado por el cliente.


Ya tengo suficiente contexto para cerrar un plan: el flujo actual acepta cualquier archivo con `audio/wav` y el test simulado incluso usa un `.txt` con MIME de WAV, así que la validación debe moverse a una comprobación de firma/cabecera real y no quedarse en la extensión. Voy a guardar el plan de trabajo y te lo dejo revisable.

Read memory

He revisado el flujo y el problema está claro: hoy la validación vive en multerConfig.js y solo confía en `mimetype`, así que un archivo falso con nombre `.wav` puede pasar. También el manejo de errores global en server.js hace que un rechazo mal propagado termine como 500 genérico.

