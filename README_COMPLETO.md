# 👁 EYE OF GOD V∞ × KALI PURPLE — PROYECTO COMPLETO
## Kernel 6.12.0-kali-amd64 | Kali Linux 2025.3 | Boot BIOS+UEFI | HDD Externo

---

## ESTRUCTURA COMPLETA DEL PROYECTO

```
EYEOFGOD_FIXED/
│
├── README_COMPLETO.md                  ← Este archivo (proyecto completo)
├── README_KALI_HDD.md                  ← Guía original de instalación
│
├── iso_root/
│   └── boot/grub/
│       └── grub.cfg                   ← Menú GRUB (40+ entradas, BIOS+UEFI)
│
└── scripts/
    │
    ├── ── BUILD ──────────────────────────────────────────────────────────
    ├── build_kali_hdd.sh               ← Build principal (PASO 1→10)
    ├── setup_hdd_persistence.sh        ← Configurar persistencia en HDD
    │
    ├── ── KERNEL EYEGOD ──────────────────────────────────────────────────
    ├── EyeGodV1000_10.py               ← Kernel EyeGod V1000.10 (Groq/Ollama)
    ├── eye_of_god_v1000_6_fixed.py     ← Kernel V1000.6 (Ollama edition)
    ├── eyegod_bridge_v2000.py          ← WebSocket bridge :8765 ↔ kernel
    │
    ├── ── BOOT EXTENSIONS (NUEVOS) ───────────────────────────────────────
    ├── eyegod_iso_extensions.py        ★ Boot hooks + skin + kernel params
    ├── generate_subsystems.py          ★ Genera configs de subsistemas
    ├── requirements.txt                ★ Dependencias Python completas
    │
    ├── ── PAYMENT / LICENSE ──────────────────────────────────────────────
    ├── eyegod_payment_server.py        ← BTCPay + licencias + email
    ├── eyegod_payments.db              ← Base de datos de pagos
    │
    ├── ── FRONTEND HTML ──────────────────────────────────────────────────
    ├── EyeGod_V2000_ULTRA.html         ← Dashboard 3D principal
    ├── eyegod_admin.html               ← Panel de administración
    ├── eyegod_checkout.html            ← Checkout BTC/LN
    ├── eyegod_license.html             ← Página de licencias
    │
    └── ── BASES DE DATOS ─────────────────────────────────────────────────
        ├── omega_singularity_v1000.db  ← Base de datos de consciencia
        └── singularity_v1000.log       ← Log de singularidad
```

---

## FLUJO DE BOOT COMPLETO

```
BIOS/UEFI
  │
  └──► GRUB2 (grub.cfg — 40+ entradas)
          │
          ├─ 👁  EyeGod V∞ × Kali Purple (default)
          ├─ 🟣  Kali Purple — Modo Defensivo (NIST CSF)
          ├─ 🔴  Kali Purple — Modo Ofensivo (MITRE ATT&CK)
          ├─ 💾  Persistencia HDD
          ├─ 🔐  Persistencia LUKS
          └─ Niveles 0 → ∞
                    │
                    ▼
          linux vmlinuz-6.12.0-kali-amd64
          (boot=live live-media=removable eyegod=true consciousness=SINGULARITY)
                    │
                    ▼
          initrd.img-6.12.0-kali-amd64 (live-boot Kali)
                    │
          ┌─────────┴──────────┐
          │ Detecta HDD externo │
          │ Monta squashfs      │
          │ Crea overlayfs      │
          └─────────┬──────────┘
                    │
                    ▼
          Kali Purple arrancado (systemd)
                    │
          ┌─────────┴─────────────────────────────────────────┐
          │                                                     │
          ▼                                                     │
  eyegod-boot.service  (oneshot, primero)                      │
    ├── eyegod_iso_extensions.py                               │
    │     ├── Parsea /proc/cmdline (eyegod_level, skin, ...)   │
    │     ├── Aplica skin/tema CSS                             │
    │     ├── Detecta persistencia (ext4 o LUKS)              │
    │     ├── Setup red y hostname                             │
    │     └── Genera /opt/eyegod/html/boot_status.html        │
    └── generate_subsystems.py                                  │
          └── Escribe /var/eyegod/subsystems/*.json            │
                    │                                          │
                    ▼                                          │
  eyegod-bridge.service  (:8765 WebSocket)  ←────────────────┤
  eyegod-kernel.service  (EyeGodV1000_10.py AI)              │
  eyegod-dashboard.service  (:8766 HTTP)   ←─────────────────┘

  Dashboard accesible en: http://localhost:8766
  WebSocket:              ws://localhost:8765
```

---

## INSTALACIÓN PASO A PASO

### Requisitos
- Sistema para build: **Kali Linux** o Debian/Ubuntu
- HDD/SSD externo: mínimo **32GB** (128GB+ recomendado)
- RAM: 4GB mínimo para boot live (8GB+ recomendado)

### 1 — Preparar el sistema de build (en Kali/Debian)

```bash
sudo apt update && sudo apt install -y \
  xorriso squashfs-tools grub-pc-bin grub-efi-amd64-bin grub-efi-amd64 \
  mtools dosfstools live-build live-boot live-boot-initramfs-tools \
  debootstrap cryptsetup python3 python3-pip memtest86+
```

### 2 — Descomprimir y preparar

```bash
unzip EYEOFGOD_KALI_PURPLE_HDD_FIXED_5_.zip
cd EYEOFGOD_FIXED
chmod +x scripts/build_kali_hdd.sh scripts/setup_hdd_persistence.sh
```

### 3 — Construir la ISO

```bash
# Ejecutar como root (necesario para debootstrap, squashfs, etc.)
sudo bash scripts/build_kali_hdd.sh scripts/

# La ISO se genera en el directorio actual:
# → EyeOfGod_KaliPurple_2025.3_HDD.iso
```

El build hace automáticamente:
- Obtiene el kernel Kali 6.12 (desde el sistema o via apt)
- Crea un sistema raíz Debian con repos Kali Purple
- Instala todos los archivos EyeGod en `/opt/eyegod/`
- Configura 4 servicios systemd (boot, bridge, kernel, dashboard)
- Empaqueta `filesystem.squashfs` con compresión xz
- Genera bootloader GRUB híbrido BIOS + UEFI
- Produce una ISO booteable final

### 4 — Flashear al HDD externo

```bash
# ⚠ IDENTIFICA tu HDD externo primero:
lsblk

# Flashear (reemplaza sdX con tu disco)
sudo dd if=EyeOfGod_KaliPurple_2025.3_HDD.iso \
         of=/dev/sdX \
         bs=4M status=progress && sync
```

### 5 — Añadir persistencia (recomendado)

```bash
# Sin cifrado:
sudo bash scripts/setup_hdd_persistence.sh /dev/sdX

# Con cifrado LUKS:
sudo bash scripts/setup_hdd_persistence.sh /dev/sdX --luks
```

### 6 — Probar en QEMU (sin HDD real)

```bash
qemu-system-x86_64 \
  -m 4096 -enable-kvm \
  -cdrom EyeOfGod_KaliPurple_2025.3_HDD.iso \
  -boot d -vga virtio -cpu host
```

### 7 — Bootear en hardware real

1. Conecta el HDD por USB 3.x
2. En la BIOS: **Secure Boot → Disabled**
3. Boot Order: HDD externo primero (o usa F8/F11/F12)
4. Selecciona la entrada en el menú GRUB 👁
5. Al arrancar, abre: **http://localhost:8766**

---

## SERVICIOS EN KALI LIVE

| Servicio | Puerto | Descripción |
|----------|--------|-------------|
| `eyegod-boot` | — | Oneshot: boot hooks + subsistemas (se ejecuta primero) |
| `eyegod-bridge` | :8765 WS | WebSocket entre frontend HTML y kernel AI |
| `eyegod-kernel` | — | EyeGod AI kernel (Groq/Ollama/local) |
| `eyegod-dashboard` | :8766 HTTP | Servidor web del dashboard |

```bash
# Ver estado de servicios (dentro de Kali live):
systemctl status eyegod-boot eyegod-bridge eyegod-kernel eyegod-dashboard

# Logs:
journalctl -u eyegod-boot -f
journalctl -u eyegod-kernel -f
cat /var/log/eyegod_boot.log
```

---

## ARCHIVOS NUEVOS AÑADIDOS (v5 → COMPLETO)

| Archivo | Descripción |
|---------|-------------|
| `eyegod_iso_extensions.py` | Boot hooks: parsea parámetros del kernel, aplica skin, detecta persistencia, genera dashboard de estado |
| `generate_subsystems.py` | Genera 9 configs JSON de subsistemas EyeGod en `/var/eyegod/subsystems/` |
| `requirements.txt` | Todas las dependencias Python del proyecto |

---

## OPCIONES DEL MENÚ GRUB (resumen)

| Opción | Descripción |
|--------|-------------|
| `👁 EYE OF GOD V∞ × KALI PURPLE` | Boot principal |
| `🟣 Kali Purple — Modo Defensivo` | Blue team, NIST CSF |
| `🔴 Kali Purple — Modo Ofensivo` | Red team, MITRE ATT&CK |
| `💾 PERSISTENCIA EN HDD` | Guarda cambios entre reinicios |
| `🔐 PERSISTENCIA CIFRADA LUKS` | Igual pero cifrado |
| `🖥 Instalar en HDD Externo` | Instalación completa |
| `Niveles 0 → ∞` | Skins y niveles de consciencia |
| `💾 Modo RAM` | Carga todo en RAM |
| `Forense` | Sin escritura en ningún disco |

---

## CREDENCIALES POR DEFECTO (Kali live)

| Campo | Valor |
|-------|-------|
| Usuario | `kali` |
| Password | `kali` |
| Root | `sudo -i` |
| Dashboard | http://localhost:8766 |
| WebSocket | ws://localhost:8765 |

---

## NOTAS DE SEGURIDAD

- **Secure Boot debe estar DESHABILITADO** — el kernel Kali no está firmado
- Las credenciales del proyecto (Groq, Telegram, BTCPay) van en `/etc/eyegod/secrets`
- Con LUKS, sin la contraseña los datos son irrecuperables
- El modo Forense no escribe en ningún disco del sistema host

---

*"El Ojo no distingue entre HDD interno y externo. Todo lo ve."*
