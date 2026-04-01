Actualización Masiva de Atributos en Microsoft 365 (PowerShell)

📌 Descripción
Este procedimiento permite actualizar el atributo Company (Empresa) de forma masiva para un grupo específico de usuarios filtrados por su dominio de correo. Es ideal para preparar el entorno antes de crear Listas de Distribución Dinámicas que dependan de este atributo.

⚙️ Requisitos Previos
Tener instalado el módulo de Exchange Online PowerShell.

Permisos de Administrador Global o Administrador de Exchange.

🚀 Procedimiento
1. Conexión a Exchange Online
Antes de comenzar, inicia sesión en el servicio:
```powershell
Connect-ExchangeOnline
```
2. Ejecución del Comando de Actualización
Utilizamos un "pipeline" para filtrar solo a los usuarios con buzón activo y asignarles el valor de empresa deseado.

```powershell
# Comando para filtrar por dominio y establecer la empresa
Get-Mailbox -ResultSize Unlimited -RecipientTypeDetails UserMailbox | Where-Object {$_.PrimarySmtpAddress -like "*@dominio.com"} | Set-User -Company "asd"
```
🔍 Desglose del Comando:
Get-Mailbox -RecipientTypeDetails UserMailbox: Filtra solo buzones de usuario humanos. Esto es clave para evitar errores de licencia, ya que solo interactúa con cuentas que tienen un buzón activo.

Where-Object { ... -like "*@dominio.com"}: Selecciona exclusivamente a los usuarios que pertenecen al dominio del cliente específico.

Set-User -Company "Nombre": Aplica el valor al atributo de organización necesario para las reglas de filtrado dinámico.
✅ Verificación
Para confirmar que los cambios se aplicaron correctamente, ejecuta el siguiente comando de consulta:

```powershell
Get-User -Filter "Company -eq 'asd'" | Select-Object DisplayName, Company, UserPrincipalName
```

💡 Notas Importantes
Propagación: Los cambios en los atributos de Azure AD / Microsoft 365 pueden tardar entre 15 y 30 minutos en ser detectados por el motor de Listas de Distribución Dinámicas.

Licenciamiento: Este método es seguro porque Get-Mailbox solo devuelve usuarios que ya tienen una licencia de Exchange asignada, evitando intentos de envío fallidos a usuarios sin buzón.
