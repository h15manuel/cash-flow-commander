

## Plan: Reorganizar dashboard con layout compacto lado a lado

### Resumen
Reorganizar las cápsulas principales del dashboard para que queden lado a lado en filas de 2, más compactas, y añadir una cápsula de "Créd. Efectivo". Además, ajustar "Efectivo Real" para que sume también los créditos en efectivo.

### Cambios en `src/pages/Index.tsx`

**1. Cápsula de estado (Caja Cuadrada):** Reducir padding a `p-2`, texto más pequeño.

**2. Monto Z y Gaveta lado a lado:** Poner ambas en un `grid grid-cols-2 gap-2`. El input de Z se compacta (texto `text-lg` en vez de `text-2xl`). La gaveta se convierte en un botón compacto similar (sin el texto "Toca para contar", solo el monto y el icono).

**3. Grid de resumen:** Pasar de 4 cápsulas a 5 (o 6) en grid de 2 columnas:
- Meta
- Efectivo Real → ahora muestra `Depósitos + Gaveta + Créd. Efectivo` y su fórmula actualizada
- Depósitos
- Propinas
- **Nueva: Créd. Efectivo** → muestra `cashCreditTotal`

**4. Ajustar "Efectivo Real":** Sumar `cashCreditTotal` al cálculo visual (nota: la lógica de cuadratura en `useAppState` ya resta los créditos en efectivo de la meta, así que para que "Efectivo Real" también los incluya, hay que decidir).

### Cambios en `src/hooks/useAppState.ts`

Actualizar `efectivoReal` para incluir `cashCreditTotal`:
```
const efectivoReal = depositsTotal + state.cashDrawer + cashCreditTotal;
```

Esto tiene sentido porque los créditos en efectivo representan dinero que está en la Z pero no se recibió como efectivo — al sumarlo al efectivo real, se compensa con la meta que ya los resta, manteniendo la cuadratura correcta.

**Espera** — revisando la lógica actual: `meta = Z - propinas - cashCredit`, `efectivoReal = depósitos + gaveta`, `diferencia = efectivoReal - meta`. Si el crédito en efectivo está en la Z pero no lo recibimos, restarlo de la meta es correcto. No debería sumarse al efectivo real porque no es dinero que tengamos. Así que la fórmula de efectivo real NO