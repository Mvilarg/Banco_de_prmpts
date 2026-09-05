# Banco de Prompts — []

Colección de prompts reutilizables, probados y documentados, organizados por caso de uso.
Cada entrada sigue la misma plantilla: **Objetivo → Elementos aplicados → Prompt → Ejemplo probado → Notas de iteración**.

## Índice

- [Resumen de textos](#resumen-de-textos)
- [Extracción de información](#extracción-de-información)
- [Clasificación de texto](#clasificación-de-texto)
- [Generación de código](#generación-de-código)

---

## Resumen de textos

**Objetivo:** Crear un artículo o documentación técnica, para uso interno del equipo de desarrollo.

**Elementos aplicados:** rol · tarea · formato · restricciones

**Prompt (plantilla):**
```
Actúa como un editor técnico especializado en documentación de software. Resume el siguiente texto en no más de 5 líneas, conservando únicamente la información técnica relevante para un desarrollador. Devuelve el resumen como una lista de viñetas.

```

**Ejemplo probado:**
- Entrada (`Node.js 22 ha sido lanzado con varias mejoras sustanciales para los desarrolladores. Entre sus características principales se encuentra el soporte nativo para cargar archivos de configuración .env sin necesidad de utilizar paquetes externos como dotenv. Además, la API integrada para la ejecución de pruebas (test runner) ha alcanzado la fase de estabilidad completa, permitiendo escribir suites de prueba sin dependencias de terceros. Por último, se implementaron optimizaciones profundas en el motor V8 que reducen la latencia del recolector de basura bajo cargas de trabajo pesadas de memoria.`): 

- Salida obtenida: 
 Node.js 22 incorpora soporte nativo para archivos .env sin librerías externas. Se estabiliza el módulo de test runner integrado.

Mejora el rendimiento del recolector de basura en cargas intensivas de memoria.

**Notas de iteración:** 
La versión 1 del prompt no pedía el formato en lista y el modelo devolvía un párrafo continuo, se agrego "Devuelve el resumen como una lista de viñetas" para solucionar el problema.
---

## Extracción de información

**Objetivo:** Extraer entidades clave (nombres, fechas, emails, montos) de correos o mensajes no estructurados y estructurarlas en formato JSON válido para consumo de apis o bases de datos.

**Elementos aplicados:** rol · tarea · formato · restricciones

**Prompt (plantilla):**
```
Actúa como un parser de datos estructurados. Extrae la información relevante del siguiente texto y devuélvela estrictamente en formato JSON válido sin texto ni explicaciones adicionales.

Campos requeridos en el JSON:
- cliente (nombre completo)
- correo (email del cliente)
- producto (nombre del ítem solicitado)
- cantidad (número entero)
- fecha_solicitada (YYYY-MM-DD o null si no se menciona) `)

```

**Ejemplo probado:**
- Entrada (`Hola equipo, mi nombre es María Fernández (maria.f@example.com) y quisiera solicitar una cotización para comprar 15 unidades del teclado mecánico Redragon K552 para la entrega del 15 de octubre.`):
- Salida obtenida:
{
  "cliente": "María Fernández",
  "correo": "maria.f@example.com",
  "producto": "teclado mecánico Redragon K552",
  "cantidad": 15,
  "fecha_solicitada": "2026-10-15"
}

**Notas de iteración:**
En la v1 el modelo incluía bloques explicativos como "Aquí tienes el JSON solicitado:". Se añadió la restricción "estrictamente en formato JSON válido sin texto ni explicaciones adicionales" para garantizar que la salida sea directamente parseable.

---

## Clasificación de texto

**Objetivo:** Categorizar tickets de soporte técnico o comentarios de clientes para automatizar su asignacion.

**Elementos aplicados:** rol · contexto · tarea · formato · restricciones

**Prompt (plantilla):**
```
Actúa como un sistema de triaje automatizado para un equipo de soporte técnico. Clasifica el siguiente ticket asignando exactamente una categoría y un nivel de prioridad de las opciones permitidas.

Opciones de categoría: [Bug, Solicitud de Función, Facturación, Consulta General]
Opciones de prioridad: [Baja, Media, Alta, Crítica]

Responde exclusivamente con el siguiente formato de 2 líneas:
Categoría: <categoria>
Prioridad: <prioridad>
```

**Ejemplo probado:**
- Entrada (`No puedo ingresar a mi cuenta desde esta mañana. Al hacer clic en login la pantalla se queda en blanco y muestra un error 500. Necesito resolverlo urgente porque no puedo procesar ventas.`):
- Salida obtenida: `Categoría: Bug Prioridad: Crítica`

**Notas de iteración:**
En pruebas iniciales el modelo clasificaba usando etiquetas inventadas como "Urgencia Máxima". Delimitar las listas de opciones válidas entre corchetes [...] garantizó valores estandarizados.
---

## Generación de código

**Objetivo:** Generar funciones auxiliares limpias, tipadas y documentadas en JavaScript/TypeScript para resolver tareas comunes de desarrollo web.

**Elementos aplicados:** Elementos aplicados:

**Prompt (plantilla):**
Actúa como un desarrollador Senior en JavaScript. Escribe una función modular para resolver la tarea descrita a continuación.

Requisitos del código:
- Usa sintaxis moderna (ES6+).
- Incluye validación de parámetros de entrada.
- Añade comentarios para tipos y valor de retorno.
- No incluyas explicaciones, solo el bloque de código documentado.


**Ejemplo probado:**
- Entrada (`Crear una función que reciba un arreglo de objetos que representan productos (con propiedades 'precio' y 'cantidad') y devuelva el total acumulado considerando un porcentaje de descuento opcional.`):
- Salida obtenida:
/**
 * Calcula el monto total acumulado de una lista de productos aplicando un descuento opcional.
 * 
 * @param {Array<{precio: number, cantidad: number}>} productos - Lista de productos a calcular.
 * @param {number} [descuento=0] - Porcentaje de descuento opcional (0 a 100).
 * @returns {number} El costo total calculado.
 * @throws {Error} Si las entradas no son válidas.
 */
function calcularTotalProductos(productos, descuento = 0) {
  if (!Array.isArray(productos)) {
    throw new Error("El parámetro productos debe ser un arreglo.");
  }
  if (typeof descuento !== "number" || descuento < 0 || descuento > 100) {
    throw new Error("El descuento debe ser un número entre 0 y 100.");
  }

  const subtotal = productos.reduce((acc, prod) => {
    const precio = Number(prod.precio) || 0;
    const cantidad = Number(prod.cantidad) || 0;
    return acc + (precio * cantidad);
  }, 0);

  const factorDescuento = (100 - descuento) / 100;
  return Number((subtotal * factorDescuento).toFixed(2));
}

**Notas de iteración:**
La v1 generaba explicaciones largas antes y después del código. Se especificó "No incluyas explicaciones, solo el bloque de código documentado" para obtener directamente el bloque ejecutable.
---


