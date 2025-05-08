# De blackbox.ai:

Para ejecutar las pruebas en un orden específico en tu archivo YAML de GitHub Actions, puedes utilizar la opción --grep de Playwright para filtrar las pruebas que deseas ejecutar. En tu caso, si tienes dos pruebas con el mismo nombre en diferentes archivos, puedes especificar el archivo de prueba que deseas ejecutar.

Aquí tienes un ejemplo de cómo modificar tu archivo YAML para ejecutar primero la prueba de buena.spec.ts y luego la de mala.spec.ts:
```yaml
name: Playwright Tests
on:
  push:
    branches: [ main, master ]
  pull_request:
    branches: [ main, master ]
jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: lts/*
    - name: Install dependencies
      run: npm ci
    - name: Install Playwright Browsers
      run: npx playwright install --with-deps
    - name: Run Playwright tests
      run: |
        # Ejecutar la prueba buena.spec.ts
        npx playwright test tests/buena.spec.ts --max-failures=1
        
        # Ejecutar la prueba mala.spec.ts
        npx playwright test tests/mala.spec.ts --max-failures=1
    - uses: actions/upload-artifact@v4
      if: ${{ !cancelled() }}
      with:
        name: playwright-report
        path: playwright-report/
        retention-days: 30
```

## Explicación de los cambios:
1. Ejecución de pruebas específicas: En lugar de ejecutar todas las pruebas con npx playwright test, se especifica el archivo de prueba que deseas ejecutar. Esto se hace con npx playwright test tests/buena.spec.ts para la primera prueba y luego npx playwright test tests/mala.spec.ts para la segunda.

2. Uso de --max-failures=1: Esto se mantiene para que la ejecución se detenga si hay un fallo en cualquiera de las pruebas.

## Consideraciones:
- Si la primera prueba (buena.spec.ts) falla, la segunda prueba (mala.spec.ts) no se ejecutará debido a la opción --max-failures=1.
- Si deseas que ambas pruebas se ejecuten independientemente de si una falla, puedes eliminar la opción --max-failures=1 de la segunda ejecución.

Con estos cambios, tu flujo de trabajo de GitHub Actions ejecutará primero la prueba de buena.spec.ts y luego la de mala.spec.ts.

## ¿Qué hace el |?
1. Indica un bloque de texto multilínea: Cuando usas |, estás diciendo que todo lo que sigue es un bloque de texto que debe ser tratado como un solo string. Esto es útil para comandos que se extienden en varias líneas.

2. Mantiene el formato: El uso de | preserva los saltos de línea y el formato del texto. Esto es especialmente útil para scripts o comandos que requieren múltiples líneas.

## Ejemplo en tu caso:
En tu archivo YAML, el bloque de comandos que sigue al | se ejecutará como un solo paso. Aquí está el fragmento relevante:
```yaml
- name: Run Playwright tests
  run: |
    # Ejecutar la prueba buena.spec.ts
    npx playwright test tests/buena.spec.ts --max-failures=1
    
    # Ejecutar la prueba mala.spec.ts
    npx playwright test tests/mala.spec.ts --max-failures=1
```

## Sin el |:
Si no usaras el |, tendrías que escribir cada comando en una línea separada con run: para cada uno, lo que haría que el archivo YAML sea más largo y menos legible. Por ejemplo:
```yaml
- name: Run Playwright tests
  run: npx playwright test tests/buena.spec.ts --max-failures=1

- name: Run Playwright tests 2
  run: npx playwright test tests/mala.spec.ts --max-failures=1
```

Línea añadida para actualizar la rama y ejecutar los tests automáticamente.
