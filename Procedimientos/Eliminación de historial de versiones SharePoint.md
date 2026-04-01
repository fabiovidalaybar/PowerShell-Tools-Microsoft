**Eliminación de historial de versiones SharePoint**
* Primero se debe conectar al modulo de pnp (ver procedimientos arriba)
* luego puedes ejecutar este script que borrará las versiones en un ciclo for (evidentemente debes cambiar las rutas acorde a lo que deseas procesar)
* este script es granular, para eliminar versiones de carpetas en específico
```PowerShell
  # 1. Variables de entorno del cliente
$siteUrl = "https://aurysconsulting.sharepoint.com/sites/2022-CMDICEconomaCirculaFaseV-Documentos"
$clientId = "96cf9290-a60e-46a3-aa8e-884b62485a8f"

# 2. Lista de subcarpetas específicas a limpiar
$carpetas = @(
    "Documentos compartidos/1. Documentos de Trabajo/8. Extras/Imágenes",
    "Documentos compartidos/1. Documentos de Trabajo/8. Extras/2212 Presentaciones",
    "Documentos compartidos/1. Documentos de Trabajo/8. Extras/2312 Presentaciones",
    "Documentos compartidos/1. Documentos de Trabajo/8. Extras/2412 Presentaciones",
    "Documentos compartidos/1. Documentos de Trabajo/8. Extras/2512 Presentaciones"
)

# 3. Conexión con el tenant de Aurys
Connect-PnPOnline -Url $siteUrl -Interactive -ClientId $clientId

# 4. Procesamiento por carpeta
foreach ($ruta in $carpetas) {
    Write-Host "`n--- Iniciando carpeta: $ruta ---" -ForegroundColor Yellow
    
    # Obtenemos los archivos dentro de la subcarpeta
    $files = Get-PnPFolderItem -FolderSiteRelativeUrl $ruta -ItemType File -Recursive -ErrorAction SilentlyContinue
    
    if ($null -eq $files) {
        Write-Host "Ruta no encontrada o carpeta vacía." -ForegroundColor Gray
        continue
    }

    foreach ($file in $files) {
        try {
            # Eliminamos versiones anteriores
            Remove-PnPFileVersion -Url $file.ServerRelativeUrl -All -Force
            Write-Host "Historial borrado: $($file.Name)" -ForegroundColor Green
        } catch {
            Write-Host "Error en $($file.Name): $($_.Exception.Message)" -ForegroundColor Red
        }
    }
}

Write-Host "`n[!] Tarea finalizada exitosamente." -ForegroundColor Cyan
```
* Con el siguiente script puedes borrar el historial de versiones de un sitio completo, esto dejará solo el último archivo

```PowerShell
# 1. Configuración de conexión
$siteUrl = "https://xxxxxxxx.sharepoint.com/sites/2022-CMDICEconomaCirculaFaseV-Documentos"
$clientId = "xxxxx-xxxx-xxxxx-xxxxxx-xxxxx"

# 2. Establecer conexión
Connect-PnPOnline -Url $siteUrl -Interactive -ClientId $clientId

# 3. Obtener todas las bibliotecas de documentos (BaseTemplate 101 es para Doc Libraries)
# Filtramos para evitar librerías ocultas o de sistema críticas
$libraries = Get-PnPList | Where-Object { $_.BaseTemplate -eq 101 -and $_.Hidden -eq $false }

Write-Host "Se encontraron $($libraries.Count) bibliotecas para procesar." -ForegroundColor Cyan

foreach ($list in $libraries) {
    Write-Host "`n>>> Procesando biblioteca: $($list.Title)" -ForegroundColor Yellow
    
    # Obtener todos los archivos de la biblioteca de forma recursiva
    # Usamos Get-PnPListItem para mayor velocidad en sitios grandes
    $items = Get-PnPListItem -List $list -PageSize 500 | Where-Object { $_.FileSystemObjectType -eq "File" }

    foreach ($item in $items) {
        try {
            # Obtenemos la URL relativa del archivo para el comando de borrado
            $fileUrl = $item["FileRef"]
            
            # Eliminamos todas las versiones excepto la actual
            Remove-PnPFileVersion -Url $fileUrl -All -Force
            Write-Host "Limpio: $($item["FileLeafRef"])" -ForegroundColor Green
        } catch {
            Write-Host "Error en $($item["FileLeafRef"]): $($_.Exception.Message)" -ForegroundColor Red
        }
    }
}

Write-Host "`n[!] Limpieza masiva completada en todo el sitio." -ForegroundColor Magenta
```
