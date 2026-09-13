# 🛡️ Evasión de Detección en Shells

Investigación y PoCs sobre técnicas de evasión de shells (AV/EDR bypass, obfuscation, encoding) con fines educativos y de research en entornos controlados.

**🌐 Demo:** https://poc-scripts-for-shell.lucaslean1806.workers.dev/

## ⚠️ Disclaimer
Este proyecto es exclusivamente para fines educativos y de investigación en seguridad ofensiva. Todo el contenido fue probado en laboratorios propios y aislados (VirtualBox/Kali). El autor no se responsabiliza por el mal uso de este material.

## 🎯 Objetivo
Documentar y analizar técnicas de evasión utilizadas para bypassear controles de detección (EDR/AV/logging) durante ejercicios de Red Team autorizados.

## 🧪 Contenido
- `index.html` — research completo (demo desplegada):
  - Nivel 1: evasión básica de puerto
  - Nivel 2: PowerShell puro sin binarios externos
  - Nivel 3: ofuscación de comandos
  - Nivel 4: shell cifrada con SSL
  - Comparativa de detección por técnica + checklist de laboratorio

## 🗺️ Roadmap
- [ ] `pocs/linux/` + `pocs/windows/`: cada PoC con header `AUTHORIZED-LAB-ONLY`, sha256 y cleanup
- [ ] `detections/sigma/`: regla por técnica (qué detecta, cómo mitigar)

## 👤 Autor

**Lucas Villagra**  
Cybersecurity Analyst | Ethical Hacker | SOC Analyst  
📍 San Miguel de Tucumán, Argentina

[![LinkedIn](https://img.shields.io/badge/LinkedIn-lucas--villagra--cybersecurity-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/lucas-villagra-cybersecurity)
[![GitHub](https://img.shields.io/badge/GitHub-Lucas18062025-181717?style=flat&logo=github)](https://github.com/Lucas18062025)
[![Portfolio](https://img.shields.io/badge/Portfolio-portafolio.lucaslean1806-00D4FF?style=flat&logo=cloudflare)](https://portafolio.lucaslean1806.workers.dev/)
