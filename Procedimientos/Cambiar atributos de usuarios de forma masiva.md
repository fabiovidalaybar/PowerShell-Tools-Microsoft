Actualización Masiva de Atributos en Microsoft 365 (PowerShell)
📌 Descripción
Este procedimiento permite actualizar el atributo Company (Empresa) de forma masiva para un grupo específico de usuarios filtrados por su dominio de correo. Es ideal para preparar el entorno antes de crear Listas de Distribución Dinámicas que dependan de este atributo.

⚙️ Requisitos Previos
Tener instalado el módulo de Exchange Online PowerShell.

Permisos de Administrador Global o Administrador de Exchange.

🚀 Procedimiento
1. Conexión a Exchange Online
Antes de comenzar, inicia sesión en el servicio:
´´´powershell
Connect-ExchangeOnline
´´´
