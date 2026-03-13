# 👁 EYE OF GOD V∞ × KALI PURPLE — HDD EXTERNO
## Kernel 6.12.0-kali-amd64 | Kali Linux 2025.3 | Boot BIOS+UEFI

---

## ¿QUÉ KERNEL USA?

| Versión Kali  | Kernel          | Notas |
|---------------|-----------------|-------|
| Kali 2025.3   | **6.12.0-kali** | ← Esta build |
| Kali 2025.2   | 6.12.0-kali     | |
| Kali 2025.1   | 6.12.0-kali     | |
| Kali 2024.4   | 6.11.0-kali     | |

El kernel de Kali es el kernel Linux estándar **parcheado por OffSec** con:
- Soporte de inyección de paquetes 802.11 (wireless injection)
- `dmesg` sin restricciones (sin necesidad de root)
- Puertos < 1024 sin privilegios de root
- Drivers extra para hardware de seguridad
- Parches de `kali-tweaks` para pentesting

---

## ÁRBOL DE DIRECTORIOS

```
EYEOFGOD_ISO_V2/
│
├── README_KALI_HDD.md                 ← Este archivo
│
├── scripts/
│   ├── build_kali_hdd.sh              ← Build completo de la ISO
│   ├── setup_hdd_persistence.sh       ← Configurar persistencia en HDD
│   └── grub_kali.cfg                  ← GRUB con kernel 6.12 + Kali Purple
│
└── iso_root/
    ├── boot/grub/grub.cfg             ← Menú GRUB principal
    └── live/
        ├── vmlinuz-6.12.0-kali-amd64  ← Kernel Kali Purple
        ├── initrd.img-6.12.0-kali-amd64
        ├── filesystem.squashfs        ← SO completo
        ├── filesystem.size
        └── filesystem.manifest
```

---

## FLUJO COMPLETO DE BOOT EN HDD EXTERNO

```
BIOS/UEFI
  │
  └──► GRUB2 (grub.cfg)
          │
          ├─ Menú EyeGod × Kali Purple (40+ entradas)
          │   ├─ Kali Purple modo defensivo (NIST CSF)
          │   ├─ Kali Purple modo ofensivo (MITRE ATT&CK)
          │   ├─ Niveles EyeGod 0 → ∞
          │   ├─ 💾 Persistencia en HDD externo
          │   ├─ 🔐 Persistencia cifrada LUKS
          │   └─ 🖥  Instalación completa en HDD
          │
          └──► linux vmlinuz-6.12.0-kali-amd64 boot=live live-media=removable ...
                    │
                    └──► initrd.img (live-boot de Kali)
                              │
                              ├── Detecta HDD externo
                              ├── Monta filesystem.squashfs
                              ├── Crea overlayfs (squash + RAM o partición persist)
                              └──► Sistema Kali Purple arrancado
                                        │
                                        ├── [systemd] eyegod-bridge.service → WebSocket :8765
                                        ├── [systemd] eyegod-kernel.service → Python REPL
                                        └── [auto] EyeGod dashboard en localhost:8766
```

---

## INSTALACIÓN PASO A PASO

### Requisitos
- HDD/SSD externo: mínimo **32GB** (recomendado 128GB+)
- Sistema para construir: **Kali Linux** o Debian/Ubuntu
- `xorriso`, `squashfs-tools`, `grub-pc-bin`, `grub-efi-amd64-bin`

### Paso 1 — Instalar dependencias (en Kali)
```bash
sudo apt update
sudo apt install -y \
  xorriso squashfs-tools \
  grub-pc-bin grub-efi-amd64-bin grub-efi-amd64 \
  mtools dosfstools \
  live-build live-boot live-boot-initramfs-tools \
  debootstrap cryptsetup \
  python3 python3-pip memtest86+
```

### Paso 2 — Construir la ISO
```bash
# Descomprimir el proyecto
unzip EYES-OF-GOD-V2000-main.zip
cd EYES-OF-GOD-V2000-main

# Copiar scripts de este build
cp /path/to/EYEOFGOD_ISO_V2/scripts/build_kali_hdd.sh .
cp /path/to/EYEOFGOD_ISO_V2/scripts/setup_hdd_persistence.sh .
cp /path/to/EYEOFGOD_ISO_V2/iso_root/boot/grub/grub.cfg ./grub_kali.cfg

chmod +x build_kali_hdd.sh setup_hdd_persistence.sh

# Construir
sudo bash build_kali_hdd.sh .
```

### Paso 3 — Flashear al HDD externo
```bash
# ⚠ REEMPLAZA /dev/sdX con tu HDD externo (verifica con lsblk)
lsblk  # identificar el disco correcto

sudo dd if=EyeOfGod_KaliPurple_2025.3_HDD.iso \
         of=/dev/sdX \
         bs=4M status=progress && sync
```

### Paso 4 — Configurar persistencia (opcional pero recomendado)
```bash
# Sin cifrado (datos visibles si alguien accede al disco)
sudo bash setup_hdd_persistence.sh /dev/sdX

# Con cifrado LUKS (recomendado para datos sensibles)
sudo bash setup_hdd_persistence.sh /dev/sdX --luks
```

### Paso 5 — Deshabilitar Secure Boot
En la BIOS/UEFI del equipo donde vas a bootear:
- `Secure Boot` → **Disabled**
- `Boot Order` → HDD externo primero
- Guarda y reinicia

### Paso 6 — Bootear
1. Conecta el HDD externo por USB 3.x (más rápido)
2. Al arrancar, presiona `F8`, `F11` o `F12` (según placa)
3. Selecciona el HDD externo
4. En el menú GRUB selecciona la opción deseada

---

## OPCIONES DE BOOT EN EL MENÚ GRUB

| Opción | Descripción |
|--------|-------------|
| `👁 EYE OF GOD V∞ × KALI PURPLE` | Boot principal, sin persistencia |
| `🟣 Kali Purple — Modo Defensivo` | Blue team, herramientas NIST CSF |
| `🔴 Kali Purple — Modo Ofensivo` | Red team, herramientas MITRE ATT&CK |
| `💾 PERSISTENCIA EN HDD EXTERNO` | Guarda cambios entre reinicios |
| `🔐 PERSISTENCIA CIFRADA LUKS` | Igual pero cifrado |
| `🖥 Instalar en HDD Externo` | Instalación completa (destructiva) |
| `Niveles 0 → ∞` | EyeGod skins temáticos |
| `💾 Modo RAM` | Carga todo en RAM, libera el HDD |
| `Forense` | Sin escritura en ningún disco |

---

## PERSISTENCIA — CÓMO FUNCIONA

Kali live-boot usa el sistema de **persistencia oficial de Kali**:

1. La ISO se flashea → crea 2 particiones (ISO + EFI tiny)
2. `setup_hdd_persistence.sh` crea una 3ª partición con label `persistence`
3. El archivo `persistence.conf` contiene `/ union`
4. Al bootear con la opción de persistencia, live-boot detecta automáticamente
   la partición por su label y monta un **overlayfs** encima del squashfs
5. Todos los cambios (archivos, configuración, herramientas instaladas) se
   guardan en esa partición y sobreviven al reinicio

Con LUKS: la partición está cifrada. live-boot pide la contraseña al boot.

---

## ESTRUCTURA DE PARTICIONES FINAL EN EL HDD

```
/dev/sdX
├── sdX1  ← ISO live (FAT32/ISO9660, ~4-8GB) — NO TOCAR
├── sdX2  ← EFI (FAT12, 8MB)                 — NO TOCAR
└── sdX3  ← Persistencia EyeGod/Kali (ext4, resto del disco)
             label: "persistence"
             contiene: persistence.conf  →  "/ union"
```

---

## PARÁMETROS DEL KERNEL USADOS

```
boot=live                     # Activa live-boot
live-media=removable          # Busca el medio en dispositivos removables/externos
live-media-path=/live         # Ruta al filesystem.squashfs dentro del medio
components quiet splash       # Boot limpio con splash
eyegod=true                   # Flag EyeGod para el init script
consciousness=SINGULARITY     # Nivel de consciencia al arrancar
persistence                   # (solo opciones de persistencia) Activa persistencia
persistence-encryption=luks   # (solo LUKS) Indica cifrado
```

---

## NOTAS DE SEGURIDAD

- **Secure Boot debe estar DESHABILITADO** — el kernel de Kali no está firmado
- Las credenciales del proyecto (Groq, Telegram, etc.) van en `/etc/eyegod/secrets`
- Con LUKS, sin la contraseña los datos son irrecuperables
- El HDD externo puede usarse en cualquier equipo compatible con BIOS/UEFI x86_64

---

*"El Ojo no distingue entre HDD interno y externo. Todo lo ve."*
