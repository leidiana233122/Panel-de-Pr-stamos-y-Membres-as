# Panel de Préstamos y Membresías (Power BI)

<details>
<summary><strong>Resumen Rápido (ES)</strong></summary>

Dashboard educativo que analiza préstamos de libros, membresías, multas y comportamiento por género literario. Incluye KPIs clave (Total Préstamos, Promedio por Miembro, % Miembros con Multa, Monto Total de Multas, Género más Popular) y visualizaciones temporales y por título.

</details>

<details>
<summary><strong>Quick Overview (EN)</strong></summary>

Educational Power BI dashboard exploring library loans, membership tiers, fines, and genre popularity. Core KPIs plus time, membership and title distribution visuals.

</details>

---

## 1. KPIs Principales / Core KPIs

| KPI                               | Descripción (ES)                                        | Description (EN)                  | Lógica / DAX Simplificado                                                                                         | Ejemplo (Dataset Demo) |
| --------------------------------- | ------------------------------------------------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ---------------------- |
| Total Préstamos                   | Cantidad total de transacciones de préstamo registradas | Total number of loan transactions | `COUNTROWS(Prestamos)`                                                                                            | 50                     |
| Promedio de Préstamos por Miembro | Intensidad promedio de uso                              | Average loans per unique member   | `DIVIDE([Total Préstamos], DISTINCTCOUNT(Miembros[MemberID]))`                                                    | 1.67                   |
| % Miembros con Multa              | % de miembros con ≥1 multa activa                       | % of members with ≥1 active fine  | `DIVIDE([Miembros con Multa],[Total Miembros])`                                                                   | 30%                    |
| Monto Total de Multas             | Suma de los importes de multas                          | Sum of fine amounts               | `SUM(Multas[Monto])`                                                                                              | 429                    |
| Total Miembros                    | Número de miembros únicos                               | Unique member count               | `DISTINCTCOUNT(Miembros[MemberID])`                                                                               | 30                     |
| Género más Popular                | Género con mayor # de préstamos                         | Genre with highest loan count     | `TOPN(1; SUMMARIZE(Prestamos; Libros[Genero]; "Loans"; COUNTROWS(Prestamos)); [Loans]; DESC)` → `SELECTEDVALUE()` | Clásico                |
| Distribución Membresías           | % Silver / Gold / Platinum                              | Membership tier distribution      | Conteo por nivel ÷ Total Miembros                                                                                 | 40% / 16.7% / 43.3%    |

> **Nota:** Los valores de ejemplo son del dataset ficticio incluido y sirven únicamente para ilustrar los cálculos.

---

## 2. Medidas DAX (Ejemplos)

```DAX
Total Prestamos = COUNTROWS(Prestamos)
Total Miembros = DISTINCTCOUNT(Miembros[MemberID])
Miembros con Multa = DISTINCTCOUNT( FILTER(Multas, Multas[Monto] > 0)[MemberID] )
Pct Miembros con Multa = DIVIDE( [Miembros con Multa], [Total Miembros] )
Promedio Prestamos por Miembro = DIVIDE( [Total Prestamos], [Total Miembros] )
Monto Total Multas = SUM(Multas[Monto])
Genero Mas Popular = 
VAR t = 
    ADDCOLUMNS( 
        SUMMARIZE(Libros, Libros[Genero]),
        "Loans", CALCULATE( COUNTROWS(Prestamos) )
    )
RETURN
    MAXX( TOPN(1, t, [Loans], DESC), Libros[Genero] )
```

---

## 3. Modelo de Datos / Data Model

**Tablas clave:**

* **Prestamos** (LoanID, LibroID, MemberID, FechaPréstamo, FechaDevolución, …)
* **Libros** (LibroID, Título, Autor, Genero, Categoría)
* **Miembros** (MemberID, Nombre, TierMembresía)
* **Multas** (FineID, MemberID, Monto, Estado, Fecha)
* (Opcional) **Calendario** para inteligencia de tiempo (`DATESYTD`, crecimiento mensual, etc.)

**Relaciones:**

* Prestamos\[LibroID] → Libros\[LibroID]
* Prestamos\[MemberID] → Miembros\[MemberID]
* Multas\[MemberID] → Miembros\[MemberID]
* Todas las tablas fact conectadas al Calendario por fecha (si se agrega).

---

## 4. Visualizaciones (Layout)

| Zona      | Visual                               | Propósito                             |
| --------- | ------------------------------------ | ------------------------------------- |
| Superior  | Tarjetas KPI                         | Mostrar métricas clave inmediatamente |
| Izquierda | Gráfico de columnas por Mes          | Tendencia temporal de préstamos       |
| Derecha   | Treemap / Barras de Género           | Popularidad relativa de géneros       |
| Centro    | Tabla de Detalle                     | Drill‑down por título / miembro       |
| Inferior  | Donut Membresías                     | Distribución de tiers                 |
| Lateral   | Filtros (Genero, Tier, Rango Fechas) | Segmentación rápida                   |

---

## 5. Interactividad

* **Slicers:** Género, Tier, Rango de Fechas.
* **Drill-through:** Desde tabla de miembros hacia detalle de multas.
* **Tooltips personalizados:** Mostrar multas acumuladas del miembro al pasar el ratón.

---

## 6. Uso / How to Reproduce

1. Importar archivos CSV (Libros, Prestamos, Miembros, Multas) a Power BI.
2. Crear tabla Calendario (si se requiere) con `CALENDARAUTO()` o rango fijo.
3. Configurar relaciones según el esquema anterior.
4. Añadir medidas DAX listadas.
5. Construir visuales y aplicar formato (paleta pastel consistente, títulos dinámicos si se usan slicers).
6. Publicar al Power BI Service y documentar versiones.

---

## 7. Próximas Mejoras / Roadmap

* Añadir medida: Crecimiento % Mes contra Mes anterior.
* Implementar segmentación por Categoría de Libro además de Género.
* Agregar página de Análisis de Multas (tiempo de resolución, top miembros con multas).
* Crear tooltip enriquecido con mini‑gráficos de historial por miembro.
* Internacionalización completa (botón para cambiar ES/EN en títulos usando dim de idioma).

---

## 8. Limitaciones

* Dataset de ejemplo de tamaño reducido: métricas pueden no reflejar patrones reales.
* No incluye dimensión de tiempo rica (festivos, estacionalidad) todavía.
* Asume que cada multa está asociada a un único miembro y no a un préstamo específico.

---

## 9. Licencia & Uso

Este proyecto es educativo. Puedes reutilizar las medidas y estructura citando esta fuente.

---

## 10. Contacto

**Autora:** Diana Montano
LinkedIn: [https://www.linkedin.com/in/montfer](https://www.linkedin.com/in/montfer)

---


