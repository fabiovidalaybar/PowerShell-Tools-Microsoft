
```powershell
   Connect-MgGraph -Scopes "Organization.Read.All" -NoWelcome
   $skus = Get-MgSubscribedSku -All -ErrorAction Stop
$urlCsv = "https://download.microsoft.com/download/e/3/e/e3e9faf2-f28b-490a-9ada-c6089a1fc5b0/Product%20names%20and%20service%20plan%20identifiers%20for%20licensing.csv"
$rutaCsv = Join-Path $env:TEMP "nombres_licencias_ms.csv"

Invoke-WebRequest -Uri $urlCsv -OutFile $rutaCsv
$tablaMs = Import-Csv $rutaCsv

$nombres = @{}
foreach ($fila in $tablaMs) {
    $nombres[$fila.String_Id] = $fila.Product_Display_Name
}

$nombres.Count
$nombres['O365_BUSINESS_PREMIUM']

$excluir = @(
    'FLOW_FREE'                # Power Automate Free
    'POWERAPPS_VIRAL'          # Power Apps Plan 2 Trial
    'POWERAPPS_DEV'            # Power Apps for Developer
    'POWER_BI_STANDARD'        # Power BI gratuito
    'DYN365_ENTERPRISE_P1_IW'  # Dynamics 365 P1 Trial
    'CCIBOTS_PRIVPREV_VIRAL'   # Copilot Studio viral/trial
    'FORMS_PRO'                # Dynamics 365 Customer Voice Trial
    'SHAREPOINTSTORAGE'        # Almacenamiento, no son licencias de usuario
    'WINDOWS_STORE'            # Residuo Microsoft Store para Empresas
)

$skusPagadas = $skus | Where-Object { $_.SkuPartNumber -notin $excluir }
$skusPagadas.Count

$skusPagadas | Select-Object SkuPartNumber,
@{Name = 'Nombre'; Expression = {
        if ($nombres[$_.SkuPartNumber]) { $nombres[$_.SkuPartNumber] }
        else { $_.SkuPartNumber }
    }
} | Format-Table -AutoSize

# --- Correcciones de nombres (la tabla de Microsoft trae algunos antiguos) ---
$correcciones = @{
    'PROJECTPREMIUM'          = 'Planner and Project Plan 5'
    'Microsoft_Teams_Premium' = 'Microsoft Teams Premium'
}
foreach ($clave in $correcciones.Keys) {
    $nombres[$clave] = $correcciones[$clave]
}

# --- Umbral para la alerta "Alto % sin uso" (0.30 = 30%) ---
$umbralSinUso = 0.30

# --- Construcción del reporte ---
$reporte = foreach ($sku in $skusPagadas) {

    $total = $sku.PrepaidUnits.Enabled
    $usadas = $sku.ConsumedUnits
    $disponibles = $total - $usadas

    # Alertas: se acumulan en una lista y al final se unen en un solo texto
    $alertas = @()
    if ($sku.PrepaidUnits.Suspended -gt 0) { $alertas += 'Suspendida / vencida' }
    if ($sku.PrepaidUnits.Warning -gt 0) { $alertas += 'En periodo de gracia' }
    if ($total -gt 0 -and $disponibles -le 0) { $alertas += 'Sin disponibles' }
    if ($total -gt 0 -and ($disponibles / $total) -gt $umbralSinUso) { $alertas += 'Alto % sin uso' }

    $nombre = if ($nombres[$sku.SkuPartNumber]) { $nombres[$sku.SkuPartNumber] } else { $sku.SkuPartNumber }

    [PSCustomObject]@{
        Licencia      = $nombre
        Codigo        = $sku.SkuPartNumber
        Total         = $total
        Usadas        = $usadas
        Disponibles   = $disponibles
        'Uso %'       = if ($total -gt 0) { [math]::Round($usadas / $total, 2) } else { 0 }
        Observaciones = $alertas -join ' | '
    }
}

$reporte | Sort-Object Total -Descending | Format-Table -AutoSize
$reporte | Select-Object Licencia, Observaciones | Format-Table -Wrap

# --- Ruta del archivo: misma carpeta del script, con la fecha en el nombre ---
$fecha = Get-Date -Format 'yyyy-MM-dd'
$rutaExcel = Join-Path $PSScriptRoot ("Licencias_M365_{0}.xlsx" -f $fecha)

# Si ya existe un archivo de hoy, se borra para generarlo limpio
if (Test-Path $rutaExcel) { Remove-Item $rutaExcel }

# --- Parámetros de Export-Excel ---
$paramsExcel = @{
    Path               = $rutaExcel
    WorksheetName      = 'Licencias'
    TableName          = 'TablaLicencias'
    TableStyle         = 'Medium2'
    AutoSize           = $true
    FreezeTopRow       = $true
    TableTotalSettings = @{ Total = 'Sum'; Usadas = 'Sum'; Disponibles = 'Sum' }
    PassThru           = $true
}

$excel = $reporte | Sort-Object Total -Descending | Export-Excel @paramsExcel

# --- Formato de porcentaje para la columna 6 (Uso %) ---
$hoja = $excel.Workbook.Worksheets['Licencias']
Set-ExcelColumn -Worksheet $hoja -Column 6 -NumberFormat '0%'
# --- Etiqueta en la primera columna de la fila de totales ---
$tabla = $hoja.Tables['TablaLicencias']
$tabla.Columns[0].TotalsRowLabel = 'TOTAL'
# --- Guardar, cerrar y abrir el archivo ---
Close-ExcelPackage $excel -Show
```
