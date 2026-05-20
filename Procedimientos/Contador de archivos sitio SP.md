```powershell
 $siteUrl = "https://gacchile.sharepoint.com/sites/Informacionhistorica"
 $clientId = "86c13266-bde9-4845-81fc-746deb5bfd47"
 Connect-PnPOnline -Url $siteUrl -Interactive -ClientId $clientId

# 1. Definimos tu ruta principal
$rutaPrincipal = "Documentos Compartidos/Proyectos cerrados/2508056 CP Pleito Guayacán"

# 2. Preparamos nuestros contadores desde cero
$global:contadorArchivos = 0
$global:contadorCarpetas = 0

Write-Host "Iniciando la búsqueda profunda. Esto puede tomar más tiempo de lo normal... ⏳" -ForegroundColor Yellow

# 3. Creamos una función especial que sabe abrir cajas dentro de cajas
Function Revisar-Carpeta {
    param (
        [string]$Ruta
    )

    # Buscar y contar los archivos en este nivel (usamos @() por si acaso está vacío)
    $archivos = @(Get-PnPFolderItem -FolderSiteRelativeUrl $Ruta -ItemType File)
    $global:contadorArchivos += $archivos.Count

    # Buscar y contar las carpetas en este nivel
    $carpetas = @(Get-PnPFolderItem -FolderSiteRelativeUrl $Ruta -ItemType Folder)
    
    # Excluimos carpetas ocultas del sistema (como "Forms") que SharePoint a veces crea
    $carpetasValidas = $carpetas | Where-Object { $_.Name -ne "Forms" }
    $global:contadorCarpetas += $carpetasValidas.Count

    # Aquí está la magia: le decimos que repita todo este proceso por cada subcarpeta encontrada
    foreach ($carpeta in $carpetasValidas) {
        $nuevaRuta = "$Ruta/$($carpeta.Name)"
        Revisar-Carpeta -Ruta $nuevaRuta
    }
}

# 4. Ponemos a trabajar nuestra función empezando por la ruta principal
Revisar-Carpeta -Ruta $rutaPrincipal

# 5. Calculamos el total absoluto
$totalAbsoluto = $global:contadorArchivos + $global:contadorCarpetas

Write-Host ""
Write-Host "✨ ¡Terminamos! Aquí está el detalle de TODO lo que tienes adentro:" -ForegroundColor Green
Write-Host "Total de archivos en todos los niveles: $global:contadorArchivos" -ForegroundColor Cyan
Write-Host "Total de subcarpetas en todos los niveles: $global:contadorCarpetas" -ForegroundColor Cyan
Write-Host "Total general absoluto (archivos + carpetas): $totalAbsoluto" -ForegroundColor Green
```
