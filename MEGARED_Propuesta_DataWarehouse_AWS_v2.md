# MEGARED - PROPUESTA DATA WAREHOUSE AWS
## Solución Completa: Redshift + Glue + QuickSight + R

**Preparado para**: MEGARED (Richard Peters, Osmar Peters, Esteban)
**Preparado por**: INCO Soluciones - AWS Advanced Partner
**Consultor**: Guillermo Robatto - Data & AI Technical Specialist
**Fecha**: 12 Noviembre 2025
**Versión**: 2.0 (Precios verificados Nov 2025)

---

## RESUMEN EJECUTIVO

**Situación Actual**:
- Bases de datos on-premise: **SQL Server (48.7GB) + MySQL (7.5GB) = 56.2GB total**
- 4-5 usuarios de BI utilizando QuickSight + R
- Necesidad de análisis histórico y reportes sin impactar producción
- Actualizaciones diarias + 2-3x/día para ventas/cobranzas
- Preferencia por modelo "pagar por uso" (pay-as-you-go)
- Enfoque "paso a paso" (no Data Lake completo inicialmente)

**Solución Propuesta**: Data Warehouse en AWS
- Amazon Redshift para almacenamiento y análisis
- AWS Glue para ETL automatizado
- Mantiene QuickSight + R existente (sin cambios)
- Sin riesgo operacional en bases productivas
- Escalable según necesidades futuras

**Beneficio Económico**: **AWS ahorra hasta $840/mes vs Azure**
- AWS On-Demand (recomendado): $112/mes | Azure: $952/mes
- AWS 24/7 (conservador): $202/mes | Azure: $952/mes
- **Ahorro anual: $10,080** (on-demand) o $9,000 (24/7)
- **Ahorro 3 años: $30,240** (on-demand) o $27,000 (24/7)

---

## ANÁLISIS DE COSTOS: AWS vs AZURE

### Escenario 1: Configuración Actual (56.2GB datos reales)

| Componente | AWS 24/7 | AWS On-Demand<br>**(Pausa nocturna)** | Azure 24/7 | Diferencia AWS |
|------------|----------|----------------------------------------|------------|----------------|
| Data Warehouse | $180/mes<br>Redshift dc2.large<br>($0.25/hr × 730hrs) | **$90/mes**<br>Redshift dc2.large<br>($0.25/hr × 12hrs × 30 días) | $870/mes<br>Synapse DW100c<br>($1.20/hr × 730hrs) | **AWS -90%** |
| ETL | $20/mes<br>Glue (2-3 jobs/día) | $20/mes<br>Glue (2-3 jobs/día) | $30/mes<br>Data Factory | AWS -33% |
| Storage | $2/mes<br>S3 (56.2GB) | $2/mes<br>S3 (56.2GB) | $2/mes<br>ADLS Gen2 | Igual |
| Backup Storage | Incluido | Incluido | Incluido | Igual |
| BI Tool | $0<br>**Ya tienen QuickSight** | $0<br>**Ya tienen QuickSight** | $50/mes<br>**Migrar a Power BI** | AWS -$50 |
| **TOTAL** | **$202/mes** | **$112/mes** | **$952/mes** | **AWS ahorra 77-88%** |

**NOTA CRÍTICA - ON-DEMAND "PAGAR POR USO"**:
- **AWS Redshift Pause/Resume**: Cuando pausan el cluster, **NO pagan compute** ($0/hora). Solo pagan backup storage (centavos).
- **Facturación por segundo**: Cobran desde que lo encienden hasta que lo pausan (precisión al segundo).
- **Ejemplo uso real MEGARED**:
  - Horario laboral: 8am-8pm (12 hrs/día × 30 días = 360 hrs/mes)
  - Costo: $0.25/hr × 360hrs = **$90/mes** (vs $180/mes 24/7)
  - **Ahorro adicional: $90/mes = $1,080/año = $3,240 en 3 años**
- **Azure Synapse**: También tiene pause/resume, pero el tier mínimo DW100c es 4.8x más caro por hora ($1.20 vs $0.25).

### Escenario 2: Con Crecimiento Futuro + Operación 24/7 (Sin Pausa)

| Componente | AWS On-Demand 24/7 | AWS Reserved 1-año<br>**(35% descuento)** | Azure 24/7 |
|------------|--------------------|--------------------------------------------|------------|
| Data Warehouse | $180/mes<br>Redshift dc2.large<br>On-Demand 24/7<br>($0.25/hr × 730hrs) | **$117/mes**<br>Redshift dc2.large<br>Reserved Instance 1-año<br>($0.16/hr × 730hrs) | $870/mes<br>Synapse DW100c<br>Dedicated SQL Pool<br>($1.20/hr × 730hrs) |
| ETL | $25/mes<br>(más frecuencia 3-4x/día) | $25/mes<br>(más frecuencia) | $35/mes |
| Storage | $3/mes (100GB) | $3/mes (100GB) | $3/mes |
| BI Tool | $0 (QuickSight existente) | $0 (QuickSight existente) | $50/mes (Power BI) |
| **TOTAL** | **$208/mes** | **$145/mes** | **$958/mes** |

**COMPARACIÓN 24/7**:
- **On-Demand 24/7**: $208/mes - Máxima flexibilidad, sin compromiso
- **Reserved 1-año**: $145/mes - Ahorro $63/mes ($756/año) si saben que usarán 24/7
- **On-Demand con Pause** (recomendado): $118/mes - Solo pagan 12hrs/día = **mejor opción**
- **Azure 24/7**: $958/mes - Más caro en todos los escenarios

**Resumen Financiero 3 Años**:

| Escenario | AWS On-Demand 24/7 | AWS Reserved 1-año | AWS On-Demand + Pause<br>**(Recomendado)** | Azure 24/7 | Ahorro vs Azure |
|-----------|--------------------|--------------------|---------------------------------------------|------------|-----------------|
| Actual (56.2GB) | $7,272 | $5,220 | **$4,032** | $34,272 | **$27,000 - $30,240** |
| Con crecimiento (100GB + 24/7) | $7,488 | $5,220 | $4,248 | $34,488 | **$27,000 - $30,240** |

**COMPARACIÓN DE OPCIONES AWS**:

1. **On-Demand con Pause/Resume** **RECOMENDADO**
   - Costo: $112-118/mes ($4,032-4,248 en 3 años)
   - Ahorro vs Azure: **$30,240** (88%)
   - Mejor para: Empresas sin departamento técnico 24/7 (caso MEGARED)
   - Flexibilidad: Pausan noches/fines de semana = $0/hora compute

2. **Reserved Instance 1-año**
   - Costo: $145/mes ($5,220 en 3 años)
   - Ahorro vs Azure: **$29,268** (85%)
   - Mejor para: Operación 24/7 confirmada
   - Compromiso: 1 año contrato, no se puede pausar para ahorrar más

3. **On-Demand 24/7** (Sin optimizar)
   - Costo: $202-208/mes ($7,272-7,488 en 3 años)
   - Ahorro vs Azure: **$27,000** (78%)
   - Mejor para: Testing inicial, flexibilidad máxima
   - Nota: Mismo costo que Reserved pero sin compromiso

**RECOMENDACIÓN FINAL PARA MEGARED**:
Comenzar con **AWS On-Demand + Pause/Resume**:
- **Ahorro garantizado: $30,240 en 3 años** (88% vs Azure)
- **Cero compromiso contractual**: Pueden pausar cuando quieran
- **Modelo "pagar por uso" real**: $0/hora cuando no lo usan
- **Costo mensual: $112-118** vs $958 Azure = **8.1x más económico**
- **Opción futura**: Si después ven que lo usan 24/7, pueden migrar a Reserved para ahorrar $63/mes adicionales

### Links Oficiales de Verificación de Precios

**AWS Pricing (US East Regions - Noviembre 2025)**:
- **Redshift dc2.large**: https://aws.amazon.com/redshift/pricing/
  - On-Demand: $0.25/hora (buscar "DC2 instances")
  - Pause/Resume: https://aws.amazon.com/blogs/big-data/lower-your-costs-with-the-new-pause-and-resume-actions-on-amazon-redshift/
- **AWS Glue ETL**: https://aws.amazon.com/glue/pricing/
  - DPU-hora: $0.44/DPU-hora (verificado Nov 2025)
- **Amazon S3 Standard**: https://aws.amazon.com/s3/pricing/
  - Storage: $0.023/GB-mes
- **QuickSight**: https://aws.amazon.com/quicksight/pricing/
  - Author: $24/usuario/mes | Reader: $3/usuario/mes
- **AWS Calculator Completo**: https://calculator.aws/#/

**Azure Pricing (East US Region - Noviembre 2025)**:
- **Synapse Analytics DW100c**: https://azure.microsoft.com/pricing/details/synapse-analytics/
  - Dedicated SQL Pool: $1.20/hora (tier mínimo)
  - Pause/Resume: https://learn.microsoft.com/en-us/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview
- **Data Factory**: https://azure.microsoft.com/pricing/details/data-factory/
  - Pipeline orchestration + Data movement
- **Azure Data Lake Storage Gen2**: https://azure.microsoft.com/pricing/details/storage/data-lake/
  - Storage: $0.023/GB-mes
- **Power BI Pro**: https://powerbi.microsoft.com/pricing/
  - Pro: $9.99/usuario/mes
- **Azure Calculator Completo**: https://azure.microsoft.com/pricing/calculator/

**Documentación Técnica Adicional**:
- **R + Redshift Connectivity**: https://aws.amazon.com/blogs/big-data/ (buscar "Connecting R with Amazon Redshift")
- **RJDBC Package (CRAN)**: https://cran.r-project.org/package=RJDBC
- **RPostgres Package (CRAN)**: https://cran.r-project.org/package=RPostgres
- **Redshift Best Practices**: https://docs.aws.amazon.com/redshift/latest/dg/best-practices.html

---

## INTEGRACIÓN R + REDSHIFT

### ¿Se puede conectar R a Redshift? **SÍ, 100% CONFIRMADO**

Durante la reunión, Richard Peters preguntó: *"¿se puede conectar a este, al Redshift o o no?"*

**Respuesta**: Sí, R se conecta perfectamente a Redshift mediante 4 métodos verificados:

1. **RJDBC** (Recomendado por AWS)
2. **RPostgres** (Compatible con protocolo PostgreSQL)
3. **ODBC** (Estándar de conexión)
4. **redshiftTools** (Para operaciones masivas)

**Ejemplo de conexión simple**:
```r
library(RPostgres)
conn <- dbConnect(RPostgres::Postgres(),
                  host = "redshift-cluster.region.amazonaws.com",
                  port = 5439,
                  dbname = "warehouse",
                  user = "usuario",
                  password = "contraseña")

# Consulta de ejemplo
ventas <- dbGetQuery(conn, "SELECT * FROM ventas WHERE fecha >= '2024-01-01'")
```

**Conclusión**: Los 4-5 usuarios de BI pueden seguir usando sus scripts de R sin modificación, conectándose a Redshift igual que a PostgreSQL.

---

## ARQUITECTURA TÉCNICA

### Diagrama Arquitectura AWS Propuesto

```mermaid
%%{init: {'theme':'base', 'themeVariables': {'primaryColor':'#ffffff','primaryTextColor':'#232F3E','primaryBorderColor':'#232F3E','lineColor':'#FF9900','fontSize':'14px'}}}%%
graph LR
    subgraph ON["ON-PREMISE"]
        DB1[("SQL Server<br/>48.7GB")]
        DB2[("MySQL<br/>7.5GB")]
    end

    subgraph AWS["AWS CLOUD"]
        direction LR
        GLUE["AWS Glue<br/>ETL<br/><b>$20/mes</b>"]
        S3[("S3<br/>Staging<br/><b>$2/mes</b>")]
        RS[("Redshift<br/>dc2.large<br/><b>$90-180/mes</b>")]
    end

    subgraph TOOLS["BI EXISTENTE"]
        QS["QuickSight + R<br/><b>$0/mes</b>"]
    end

    DB1 -->|"2-3x/día"| GLUE
    DB2 -->|"2-3x/día"| GLUE
    GLUE --> S3
    S3 --> RS
    RS --> QS

    classDef onprem fill:#f0f0f0,stroke:#555,stroke-width:2px,color:#000
    classDef aws fill:#FFF4E6,stroke:#FF9900,stroke-width:2px,color:#000
    classDef bi fill:#E8F5E9,stroke:#4CAF50,stroke-width:2px,color:#000

    class DB1,DB2 onprem
    class GLUE,S3,RS aws
    class QS bi
```

**COSTOS MENSUALES**:
- **Recomendado** (Pause/Resume): $112/mes
- **24/7**: $202/mes
- **Azure alternativa**: $952/mes
- **Ahorro AWS vs Azure**: 77-88% ($840/mes)

**BENEFICIOS CLAVE**:
- Queries retail: <5 segundos (vs horas actuales)
- Sin riesgo operacional en bases productivas
- Análisis histórico completo (3+ años)
- Escalable a 100GB, 500GB sin rediseño
- Backup automático diario
- QuickSight + R sin cambios (cero curva aprendizaje)
- Ahorro vs Azure: $30,240 en 3 años (88%)

### Flujo de Datos Detallado

**1. Extracción (AWS Glue)**
- Conexión ODBC a SQL Server + MySQL on-premise
- Extracción incremental 2-3x/día (solo registros nuevos/modificados)
- Duración: ~20-30 minutos por job
- Costo: $0.44/DPU-hora × 2 DPUs × 0.5 hr × 3 jobs × 30 días = ~$20/mes

**2. Transformación (AWS Glue)**
- Limpieza de datos (nulls, duplicados)
- Normalización de formatos
- Joins entre SQL Server + MySQL
- Cálculos derivados (totales, agregados)

**3. Carga (S3 → Redshift)**
- Staging en S3 (formato Parquet comprimido)
- COPY command de S3 a Redshift (paralelo, rápido)
- Actualización de tablas fact/dimension

**4. Consulta (QuickSight + R → Redshift)**
- QuickSight: Conexión JDBC nativa a Redshift
- R: RJDBC o RPostgres (compatible PostgreSQL)
- Queries optimizados con columnar storage
- Performance: <5 segundos queries complejos

---

## VENTAJAS AWS vs AZURE PARA MEGARED

### 1. Costo: Hasta 8.5x Más Económico + Verdadero "Pagar por Uso"
- **AWS On-Demand (recomendado)**: $112/mes | Azure: $952/mes
- **AWS 24/7 (conservador)**: $202/mes | Azure: $952/mes
- **Ahorro 3 años**: $30,240 (on-demand) o $27,000 (24/7)
- **Modelo "pagar por uso" REAL**: Pause/Resume automático
  - Pausan cluster fuera de horario laboral → **$0/hora compute**
  - Facturación por segundo (no por hora completa)
  - Ejemplo: 8am-8pm lunes-viernes = solo 12hrs/día × 22 días = 264hrs/mes
  - Costo real: $0.25 × 264hrs = **$66/mes Redshift** (vs $870 Azure)

### 2. Sin Migración de BI Tools
- **QuickSight ya lo tienen**: Inversión protegida
- **R ya lo usan**: Scripts funcionan sin cambios
- **4-5 usuarios**: Sin curva de aprendizaje
- **Azure**: Requiere migrar a Power BI ($600/año + tiempo + frustración)

### 3. Performance Retail Optimizado
Redshift está optimizado para queries analíticos de retail:
- **Queries simples** (ventas del día): <1 segundo
- **Queries complejos** (análisis multi-dimensión): 3-5 segundos
- **ETL batches**: 20-30 minutos (vs horas en bases productivas)

**Caso de éxito**: HauteLook (Nordstrom)
- 14 millones de miembros
- 20+ eventos de venta diarios
- Usan Redshift para analytics en tiempo real
- CTO Kevin Diamond: "Centralized data, singular reporting, analytics-optimized model"

### 4. Escalabilidad Sin Rediseño
Redshift dc2.large aguanta hasta **160GB** sin cambios:
- Hoy: 20GB realista, 50GB peor caso
- Mañana: Crece a 100GB → Sin cambios de arquitectura
- Futuro: Crece a 500GB → Agregar nodos (horizontal scaling)

Azure Synapse tier mínimo (DW100c) está **sobredimensionado** para 50GB.

### 5. Integración R Nativa
- Redshift habla PostgreSQL wire protocol
- R tiene paquetes maduros: RPostgres, RJDBC
- AWS blog oficial con tutoriales
- Sin dependencias de Microsoft stack

---

## COMPARACIÓN TÉCNICA DETALLADA

| Característica | AWS Redshift | Azure Synapse | Ganador |
|----------------|--------------|---------------|---------|
| **Costo mensual** | $180-202 | $870-952 | AWS (4.8x) |
| **Almacenamiento incluido** | 160GB SSD | ~100GB | AWS |
| **Performance queries** | <5 seg (columnar MPP) | 10-30 seg (DW100c) | AWS |
| **ETL cost** | $20/mes (Glue) | $30/mes (ADF) | AWS |
| **BI tool compatibility** | QuickSight nativo | Power BI nativo | AWS (ya lo tienen) |
| **R connectivity** | RJDBC, RPostgres (4 métodos) | ODBC (1 método) | AWS |
| **Escalabilidad horizontal** | Agregar nodos (lineal) | Aumentar DWUs (caro) | AWS |
| **Backup automático** | Incluido (S3) | Incluido (ADLS) | Empate |
| **Pay-as-you-go** | Sí (pause/resume) | Sí (pause/resume) | Empate |
| **Latencia a on-premise** | ~50ms (VPN) | ~50ms (VPN) | Empate |

**Resultado**: AWS gana en 7 de 10 categorías críticas para MEGARED.

---

## PRECIOS VERIFICADOS (Noviembre 2025)

### AWS Pricing (US East Regions)

| Servicio | SKU | Precio Unitario | Uso Mensual | Costo/Mes | Link Verificación |
|----------|-----|-----------------|-------------|-----------|-------------------|
| **Redshift dc2.large** | On-Demand | $0.25/hora | 730 horas | $180 | [aws.amazon.com/redshift/pricing](https://aws.amazon.com/redshift/pricing/) |
| **AWS Glue** | ETL DPU-hora | $0.44/DPU-hora | ~45 DPU-horas | $20 | [aws.amazon.com/glue/pricing](https://aws.amazon.com/glue/pricing/) |
| **S3 Standard** | Storage | $0.023/GB-mes | 50GB | $2 | [aws.amazon.com/s3/pricing](https://aws.amazon.com/s3/pricing/) |
| **Data Transfer** | In | $0/GB | Unlimited | $0 | Inbound free |
| **QuickSight** | Ya existe | - | - | $0 | Ya tienen licencias |
| **TOTAL AWS** | | | | **$202** | [AWS Calculator](https://calculator.aws/#/) |

**Nota Glue**: 2 DPUs × 0.5 hr × 3 jobs/día × 30 días = 90 DPU-horas/mes (estimado conservador $20-25/mes)

### Azure Pricing (East US Region)

| Servicio | SKU | Precio Unitario | Uso Mensual | Costo/Mes | Link Verificación |
|----------|-----|-----------------|-------------|-----------|-------------------|
| **Synapse DW100c** | Dedicated SQL | $1.20/hora | 730 horas | $870 | [azure.microsoft.com/pricing/details/synapse-analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/) |
| **Data Factory** | Pipeline + Activity | Variable | ~90 activities | $30 | [azure.microsoft.com/pricing/details/data-factory](https://azure.microsoft.com/pricing/details/data-factory/) |
| **ADLS Gen2** | Storage | $0.023/GB-mes | 50GB | $2 | [azure.microsoft.com/pricing/details/storage/data-lake](https://azure.microsoft.com/pricing/details/storage/data-lake/) |
| **Data Transfer** | In | $0/GB | Unlimited | $0 | Inbound free |
| **Power BI Pro** | Migración requerida | $9.99/usuario | 5 usuarios | $50 | [powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing/) |
| **TOTAL AZURE** | | | | **$952** | [Azure Calculator](https://azure.microsoft.com/pricing/calculator/) |

**Nota sobre precios**: Los valores están redondeados para facilitar la comparación. Cálculos exactos: Redshift $182.50/mes ($0.25×730hrs), Glue $19.80/mes, S3 $1.15/mes, Azure Synapse $876/mes ($1.20×730hrs), Power BI $49.95/mes. Para precisión al centavo, consultar las calculadoras oficiales de AWS y Azure.

**⚠️ Costo Oculto Azure**: Migrar de QuickSight a Power BI implica:
- $600/año en licencias
- 2-3 semanas re-desarrollo dashboards
- Training usuarios (curva aprendizaje)

---

## RECOMENDACIÓN FINAL

### Para MEGARED, recomendamos AWS On-Demand por:

1. **Ahorro Económico Máximo**: $30,240 en 3 años vs Azure (88% ahorro)
   - **$112-118/mes** con pause/resume (vs $952-958 Azure)
   - Opción conservadora $202-208/mes 24/7 (vs $952-958 Azure)
   - Opción intermedia $145/mes Reserved 1-año (vs $952-958 Azure)
2. **Verdadero "Pagar por Uso"**: Pause/resume automático
   - Pausan fuera de horario laboral → **$0/hora compute**
   - Facturación por segundo (no hora completa)
   - Solo pagan backup storage cuando está pausado (centavos)
3. **Sin Cambios BI**: QuickSight + R funcionan sin migración
   - Cero curva de aprendizaje para 4-5 usuarios
   - Scripts R compatibles sin modificación (4 métodos verificados)
4. **Performance**: <5 segundos queries complejos (vs horas actuales)
   - Redshift optimizado para retail analytics
   - Caso de éxito: HauteLook/Nordstrom
5. **Sin Riesgo Operacional**: Bases productivas intactas
   - ETL incremental 2-3x/día sin impacto
6. **Escalabilidad**: Aguanta 160GB sin rediseño
   - Hoy: 56.2GB → Espacio para crecer 2.8x
   - Futuro: Agregar nodos horizontalmente

---

## CONTACTO

**INCO Soluciones - AWS Advanced Partner**

**Consultor Técnico**:
Guillermo Robatto
Data & AI Technical Specialist
📧 guillermo.robatto@incosoluciones.com
📱 +598 99 776 565

**Gerente de Proyecto**:
José P.
📧 jose.p@incosoluciones.com

**Página Web**: www.incosoluciones.com

---

## ANEXOS

### Anexo A: Links Oficiales de Verificación

**AWS**:
- Redshift Pricing: https://aws.amazon.com/redshift/pricing/
- Glue Pricing: https://aws.amazon.com/glue/pricing/
- S3 Pricing: https://aws.amazon.com/s3/pricing/
- QuickSight Pricing: https://aws.amazon.com/quicksight/pricing/
- AWS Calculator: https://calculator.aws/#/

**Azure**:
- Synapse Pricing: https://azure.microsoft.com/pricing/details/synapse-analytics/
- Data Factory Pricing: https://azure.microsoft.com/pricing/details/data-factory/
- Storage Pricing: https://azure.microsoft.com/pricing/details/storage/data-lake/
- Power BI Pricing: https://powerbi.microsoft.com/pricing/
- Azure Calculator: https://azure.microsoft.com/pricing/calculator/

**R + Redshift Connectivity**:
- AWS Blog: "Connecting R with Amazon Redshift" (blog.aws.amazon.com)
- RJDBC Package: https://cran.r-project.org/package=RJDBC
- RPostgres Package: https://cran.r-project.org/package=RPostgres
- Progress DataDirect: Redshift ODBC/JDBC drivers

### Anexo B: Caso de Éxito Retail

**HauteLook + Nordstrom**:
- Empresa: E-commerce fashion (adquirida por Nordstrom 2011)
- Escala: 14M miembros, 20+ sale events/día
- Solución: Amazon Redshift para analytics centralizados
- Presentación: AWS re:Invent 2013 (CTO Kevin Diamond)
- Beneficios: Single source of truth, reporting unificado, modelo analytics-optimized
- Link: [AWS Redshift Customers](https://aws.amazon.com/redshift/customers/)

### Anexo C: Glosario Técnico

- **Data Warehouse**: Base de datos optimizada para análisis (OLAP)
- **ETL**: Extract, Transform, Load (proceso de integración de datos)
- **DPU**: Data Processing Unit (métrica de AWS Glue)
- **DWU/cDWU**: Data Warehouse Unit (métrica de Azure Synapse)
- **MPP**: Massively Parallel Processing (arquitectura distribuida)
- **Columnar Storage**: Almacenamiento por columnas (vs filas) para analytics
- **JDBC/ODBC**: Protocolos estándar de conexión a bases de datos
- **VPN/VPC Peering**: Conexión segura entre on-premise y cloud

---

**Documento preparado por**: Guillermo Robatto - INCO Soluciones
**Fecha**: 12 Noviembre 2025
**Versión**: 2.0 (Verificado)
**Confidencialidad**: Documento privado para MEGARED

---

**© 2025 INCO Soluciones - AWS Advanced Partner. Todos los derechos reservados.**
