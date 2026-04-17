# Procedimiento: Creación Masiva de Lista de Distribución con Usuarios Externos (M365)

Este documento detalla el proceso técnico para crear una lista de distribución en Microsoft 365 que incluya contactos externos (Gmail, Outlook, etc.) de forma masiva utilizando PowerShell.

## Requisitos Previos

1.  **Módulo de PowerShell:** Tener instalado el módulo `ExchangeOnlineManagement`.
2.  **Archivo de Datos:** Un archivo `.csv` con las columnas: `Name`, `DisplayName`, `ExternalEmailAddress`.
3.  **Permisos:** Credenciales de Administrador Global o Administrador de Exchange.

## Paso 1: Conexión a Exchange Online

Abrir una consola de PowerShell como administrador y ejecutar:

```powershell
Connect-ExchangeOnline
```
Paso 2: Creación de la Lista de Distribución (Contenedor)
Antes de agregar miembros, es necesario crear la lista de distribución. Reemplazar los valores según corresponda:
```powershell
New-DistributionGroup -Name "Nombre de la Lista" `
                      -Alias "alias.lista" `
                      -PrimarySmtpAddress "correo.lista@dominio.com" `
                      -Type "Distribution"
```

Paso 3: Importación Masiva de Contactos y Miembros
El siguiente script realiza dos funciones: crea el MailContact (si no existe) y lo agrega como miembro a la lista de distribución.
```powershell
# 1. Importar datos del CSV
$usuarios = Import-Csv "C:\Ruta\TuArchivo.csv"

# 2. Definir el correo de la lista de distribución
$listaDestino = "correo.lista@dominio.com"

foreach ($linea in $usuarios) {
    $emailInvitado = $linea.ExternalEmailAddress
    
    # Verificar si el contacto ya existe en el sistema
    $existe = Get-Recipient -Identity $emailInvitado -ErrorAction SilentlyContinue

    if ($null -eq $existe) {
        # Crear el contacto externo si no existe
        New-MailContact -Name $linea.Name -DisplayName $linea.DisplayName -ExternalEmailAddress $emailInvitado
    }

    # Agregar el contacto a la lista de distribución
    try {
        Add-DistributionGroupMember -Identity $listaDestino -Member $emailInvitado -ErrorAction Stop
    } catch {
        Write-Host "Aviso: No se pudo agregar $emailInvitado (posiblemente ya es miembro)." -ForegroundColor Yellow
    }
}
```

Notas Adicionales
Sincronización: Los cambios pueden tardar hasta 60 minutos en propagarse totalmente en el entorno de Microsoft 365.

Gestión de Errores: El uso de Get-Recipient previene errores por duplicidad de direcciones proxy en el tenant.
