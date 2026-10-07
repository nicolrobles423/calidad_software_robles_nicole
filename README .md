## Autor:
### Nicole Robles

# 🧪 Calidad de Software

Repositorio de la materia **Calidad de Software**: conceptos básicos, buenas prácticas y ejemplos prácticos de **pruebas unitarias** y **pruebas End-to-End (E2E)**.

---

## 📑 Contenido

1. [Conceptos básicos](#-conceptos-básicos)
2. [Niveles de prueba](#-niveles-de-prueba)
3. [Pruebas unitarias](#-pruebas-unitarias)
4. [Pruebas E2E](#-pruebas-end-to-end-e2e)
5. [Estructura del repositorio](#-estructura-del-repositorio)
6. [Instalación y ejecución](#-instalación-y-ejecución)
7. [Buenas prácticas](#-buenas-prácticas)

---

## 📘 Conceptos básicos

| Concepto | Definición breve |
|---|---|
| **Calidad de software** | Grado en que un sistema cumple sus requisitos y las expectativas del usuario. |
| **ISO/IEC 25010** | Modelo de calidad que define características como funcionalidad, rendimiento, usabilidad, fiabilidad, seguridad, mantenibilidad y portabilidad. |
| **Verificación** | ¿Estamos construyendo el producto **correctamente**? (cumple la especificación). |
| **Validación** | ¿Estamos construyendo el producto **correcto**? (satisface al usuario). |
| **Error** | Equivocación humana al desarrollar. |
| **Defecto (bug)** | Falla en el código producida por un error. |
| **Falla** | Comportamiento incorrecto observable al ejecutar el sistema. |
| **Caso de prueba** | Conjunto de entradas, condiciones y resultado esperado. |
| **Aseguramiento de calidad (QA)** | Procesos para **prevenir** defectos. |
| **Control de calidad (QC)** | Actividades para **detectar** defectos en el producto. |
| **Cobertura de código** | Porcentaje del código ejecutado por las pruebas. |
| **Prueba de regresión** | Verifica que los cambios nuevos no rompan lo que ya funcionaba. |

### Tipos de prueba según el enfoque

- **Caja negra:** se prueba la funcionalidad sin conocer el código interno.
- **Caja blanca:** se prueba conociendo la lógica interna (rutas, condiciones, bucles).
- **Funcionales:** validan *qué* hace el sistema.
- **No funcionales:** validan *cómo* lo hace (rendimiento, seguridad, usabilidad).

---

## 🔺 Niveles de prueba

```
        /\
       /E2E\        ← Pocas, lentas, prueban el flujo completo
      /------\
     /Integración\  ← Prueban la comunicación entre módulos
    /------------\
   /   Unitarias  \ ← Muchas, rápidas, prueban funciones aisladas
  /________________\
```

> **Pirámide de pruebas:** la base debe tener muchas pruebas unitarias y la punta pocas pruebas E2E, porque estas últimas son más costosas y lentas.

---

## 🧩 Pruebas unitarias

Verifican **la unidad más pequeña de código** (una función, método o componente) de forma **aislada**.

**Características:**
- Rápidas y automatizadas.
- Independientes entre sí.
- Usan *mocks* o *stubs* para simular dependencias externas.

**Patrón AAA:**
- **Arrange** → preparar datos.
- **Act** → ejecutar la función.
- **Assert** → comprobar el resultado.

### Ejemplo con Jest (JavaScript)

`src/calculadora.js`
```js
function sumar(a, b) {
  return a + b;
}

function dividir(a, b) {
  if (b === 0) throw new Error("No se puede dividir para cero");
  return a / b;
}

module.exports = { sumar, dividir };
```

`tests/unit/calculadora.test.js`
```js
const { sumar, dividir } = require("../../src/calculadora");

describe("Calculadora", () => {
  test("suma dos números", () => {
    // Arrange
    const a = 2, b = 3;
    // Act
    const resultado = sumar(a, b);
    // Assert
    expect(resultado).toBe(5);
  });

  test("divide correctamente", () => {
    expect(dividir(10, 2)).toBe(5);
  });

  test("lanza error al dividir para cero", () => {
    expect(() => dividir(5, 0)).toThrow("No se puede dividir para cero");
  });
});
```

Ejecutar:
```bash
npm test
npm test -- --coverage   # con reporte de cobertura
```

---

## 🌐 Pruebas End-to-End (E2E)

Simulan el comportamiento de un **usuario real**, probando el sistema **completo** de inicio a fin: interfaz, backend y base de datos.

**Características:**
- Se ejecutan en un navegador real o automatizado.
- Validan flujos completos (ej. login, registro, compra).
- Son más lentas y frágiles que las unitarias.

### Ejemplo con Cypress

`cypress/e2e/login.cy.js`
```js
describe("Flujo de inicio de sesión", () => {
  beforeEach(() => {
    cy.visit("http://localhost:3000/login");
  });

  it("inicia sesión con credenciales válidas", () => {
    cy.get("input[name=email]").type("usuario@test.com");
    cy.get("input[name=password]").type("123456");
    cy.get("button[type=submit]").click();

    cy.url().should("include", "/dashboard");
    cy.contains("Bienvenido").should("be.visible");
  });

  it("muestra error con credenciales inválidas", () => {
    cy.get("input[name=email]").type("malo@test.com");
    cy.get("input[name=password]").type("incorrecta");
    cy.get("button[type=submit]").click();

    cy.contains("Credenciales incorrectas").should("be.visible");
  });
});
```

Ejecutar:
```bash
npx cypress open   # modo interactivo
npx cypress run    # modo consola (headless)
```

### Unitarias vs E2E

| Aspecto | Unitarias | E2E |
|---|---|---|
| Alcance | Una función o componente | Todo el sistema |
| Velocidad | Muy rápidas | Lentas |
| Costo de mantenimiento | Bajo | Alto |
| Dependencias | Simuladas (mocks) | Reales |
| Detectan | Errores de lógica | Errores de integración y flujo |
| Herramientas | Jest, Vitest, JUnit, PyTest | Cypress, Playwright, Selenium |

---

## 📂 Estructura del repositorio

```
calidad-de-software/
├── src/                  # Código fuente a probar
│   └── calculadora.js
├── tests/
│   └── unit/             # Pruebas unitarias
│       └── calculadora.test.js
├── cypress/
│   └── e2e/              # Pruebas End-to-End
│       └── login.cy.js
├── package.json
└── README.md
```

---

## ⚙️ Instalación y ejecución

**Requisitos:** Node.js 18 o superior y npm.

```bash
# 1. Clonar el repositorio
git clone https://github.com/<usuario>/calidad-de-software.git
cd calidad-de-software

# 2. Instalar dependencias
npm install

# 3. Instalar herramientas de prueba (si no están en package.json)
npm install --save-dev jest cypress
```

Scripts sugeridos en `package.json`:
```json
"scripts": {
  "test": "jest",
  "test:coverage": "jest --coverage",
  "e2e": "cypress run",
  "e2e:open": "cypress open"
}
```

---

## ✅ Buenas prácticas

- Escribir pruebas **pequeñas, claras y con un solo objetivo**.
- Usar nombres descriptivos: *"debería lanzar error al dividir para cero"*.
- Mantener las pruebas **independientes** (no depender del orden de ejecución).
- Probar **casos límite**: valores vacíos, nulos, negativos, cero.
- Ejecutar las pruebas en cada cambio (integración continua / CI).
- Buscar una buena cobertura, pero priorizar **calidad sobre cantidad** de pruebas.

---

## 👤 Autor

**Romeo Robles** — Estudiante de Desarrollo de Software, Universidad Tecnológica Equinoccial (UTE).

📚 Materia: Calidad de Software
