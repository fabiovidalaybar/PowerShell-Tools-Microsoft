# ***Procedimientos Microsoft: PowerShell***
**Autor:** Fabio Andrés Vidal Aybar

Este repositorio contiene una guía detallada sobre la gestión de módulos, conexiones y procedimientos específicos para la administración de entornos Microsoft mediante PowerShell[cite: 8].

---
```powershell

```
## Módulos y conexiones

### Notas importantes:
* Reinstalar módulos no corrompe los archivos; de hecho, sirve para actualizarlos.
* Existen modulos que funcionan con V7 y otros solo con V5

### Instalación de módulos y conexiones
* Para instalar los módulos es necesario tener instalada y actualizadas las herramientas de PowerShellGet y PackageManagement, a estos se les llama “gestores de paquete”  y sirven precisamente para eso, para instalar los módulos. 
Pero antes de eso debes entender que para que powershell se pueda comunicar con repositorios externos debes tener actualizado “Nuget” ya que éste es el motor que permite esa comunicación. Por lo que esto es lo primero que debes instalar antes de todo:
```powershell
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Install-Module -Name PowerShellGet -Force -AllowClobber -SkipPublisherCheck
```
#### Scope: Scope sirve principalmente para saltarse restricciones de archivos del sistema:
```powershell
Install-Module -Name PowerShellGet -Scope CurrentUser -Force -AllowClobber
```
#### Exchange
```powershell
Install-Module -Name ExchangeOnlineManagement -Force -AllowClobber -Scope CurrentUser
Import-Module ExchangeOnlineManagement
```
```powershell
Connect-ExchangeOnline
```
## O
```powershell
Connect-ExchangeOnline -UserPrincipalName vedata@aurysconsulting.com
```
* Commando para desconectarse al final de la sesión:
```powershell
Disconnect-ExchangeOnline
```
### Instalar e importar módulo PnP
```powershell
Install-Module PnP.PowerShell
Import-Module PnP.PowerShell
```
* A finales de 2024 por razones de seguridad. Ahora, cada tenant debe registrar su propia aplicación en Entra ID para que el módulo funcione.
  para registrar tu propia aplicación en Entra ID
  ```powershell
  Register-PnPEntraIDAppForInteractiveLogin -ApplicationName "PnP PowerShell Aurys" -Tenant escribirnombredeltenant
  ```
  * ### Qué pasará ahora:
    * Se abrirá una ventana de inicio de sesión de Microsoft.
    * Entra con tus credenciales de admin.
    * Te aparecerá una pantalla de "Permisos solicitados". Debes marcar la casilla "Consentimiento en nombre de su organización" y hacer clic en Aceptar.
    * Una vez termine, la consola te mostrará un Client ID (un código largo de letras y números). Cópialo y guárdalo, lo necesitaremos ahora.

  * Ahora que tienes tu propia aplicación registrada, usa ese ID para conectar. Reemplaza TU-CLIENT-ID-AQUÍ por el código que obtuviste en el paso anterior:
```powershell
$siteUrl = "url de la pagina de SP a la que te quieres conectar"
$clientId = "TU-CLIENT-ID-AQUÍ"

Connect-PnPOnline -Url $siteUrl -Interactive -ClientId $clientId
```
### Sharepoint
> [!Nota]
> Para conectarse a SP, se debe usar PowerShell 5.1
```powershell
Install-Module -Name Microsoft.Online.SharePoint.PowerShell -Force
Import-Module Microsoft.Online.SharePoint.PowerShell
```

### Conectarse a Sharepoint
```powershell
Connect-SPOService -Url https://xxx-admin.sharepoint.com
```
## Utilidades
### Comandos Base

Variables:  
**Tipo String**  

> [!IMPORTANT]
> Las comillas simples y dobles cambian el contexto, ojito con eso.


$nombre_de_la_variable = "fabio"  
Ejemplo:  
$nombre = "Fabio"  
$mensaje = "Hola $nombre"   
El resultado será: Hola Fabio    
$nombre = "Fabio"    
$mensaje = 'Hola $nombre'     
El resultado será: Hola $nombre (literalmente)

**Tipo Lista**    
$nombre_de_la_variable = @(1, 2, 3, “A”)    


# **Procedimientos**

**Ajuste Visual de Horarios en Calendarios de Sala**  
1. El Problema: El "Bloqueo Gris" en Outlook  
Cuando un usuario intenta agendar una sala y ve que el fondo del calendario cambia de color (normalmente a gris) a partir de cierta hora, no siempre significa que la sala esté ocupada. Generalmente, esto indica que se ha alcanzado el límite de las Horas Laborales (Working Hours) configuradas en el buzón del recurso.  
2. El Comando de Solución  
Para corregir esto, se utiliza el cmdlet de Exchange Online que modifica la configuración visual del calendario:  
```PowerShell
Set-MailboxCalendarConfiguration -Identity "sala@dominio.com" -WorkingHoursStartTime 08:00:00 -WorkingHoursEndTime 20:00:00 -WorkingHoursTimeZone "SA Pacific Standard Time"
```
3. Desglose de Parámetros  
Parámetro	Función	Importancia  
-Identity	Identifica el correo electrónico de la sala.	Es el "sujeto" de la acción.  
**-WorkingHoursStartTime**:	Define cuándo se "ilumina" el calendario en la mañana.	Evita que el calendario se vea gris antes de empezar la jornada.  
**-WorkingHoursEndTime**:	Define cuándo vuelve a sombrearse el calendario en la tarde.	Al extenderlo (ej. a las 20:00), se eliminan las restricciones visuales para agendar tarde.  
**-WorkingHoursTimeZone**:	Asegura que el horario se base en la zona horaria local.	Para Chile, se usa "SA Pacific Standard Time" para evitar desfases.  


**Eliminación de historial de versiones Share Point**
* Primero se debe conectar al modulo de pnp (ver procedimientos arriba)
* luego puedes ejecutar este script que borrará las versiones en un ciclo for (evidentemente debes cambiar las rutas acorde a lo que deseas procesar)
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
