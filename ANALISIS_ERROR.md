# Analisis del Error - frmExportarCaptura.cargarInformes

## Error Principal

```
System.ArgumentException: No coincide el numero de parametros del procedimiento
almacenado O02DMRE0.T_EBE.usp_GET_PUNTO_ENTRADA_ESTADO
```

**Ubicacion en el log**: `log.2026-04-08.0.txt`, lineas 2968-2976 y 3173-3181

## Causa Raiz

El metodo `GestorEstado.GetPuntoEntradaEstado()` envia un numero de parametros que **no coincide** con los que define el stored procedure `O02DMRE0.T_EBE.usp_GET_PUNTO_ENTRADA_ESTADO` en Oracle.

El error de WCF (`CommunicationObjectFaultedException`) es un **efecto secundario**: tras el primer fallo, el canal WCF queda en estado "Faulted" y todos los reintentos posteriores fallan con ese error.

## Cadena de Llamadas

```
frmExportarCaptura.cargarInformes()
  -> GestorEstado.GetPuntoEntradaEstado(idEstado, fecha, tipo_grupo, entidad_supervisora, puntoEntradaSeleccionado)  [5 parametros]
    -> DBManager.ExecuteDataSet(cadenaCon, nombrePA, ParamValue[])
      -> DBManager.ExecuteDatasetConTimeout(conexion, nombrePA, timeout, valoresParam[])
        -> DBManager.assignParameterValues(cmdParameters, parameterValues, nombrePA)  ** FALLA AQUI **
```

## Solucion

1. **Revisar en Oracle** la firma actual del SP `O02DMRE0.T_EBE.usp_GET_PUNTO_ENTRADA_ESTADO` (numero y tipo de parametros).
2. **Revisar en el codigo C#** que parametros pasa `GetPuntoEntradaEstado()` a `ExecuteDataSet()`.
3. **Sincronizar** ambos lados para que el numero de parametros coincida.

## Datos del Incidente

| Campo | Valor |
|-------|-------|
| Usuario | JORGE VICIEDO MOMPO |
| Sesiones | 69335, 69340 |
| Fecha | 07/04/2026 |
| Horas | 15:49:32 y 15:59:24 |
| Version cliente | 4.47.0.18 |
| Entorno | SIRBE PROD |
| Equipo | A0202170 |
| IP | 172.22.64.44 |
