# Kompletní Nastavení Serverových Disků

Tento README dokumentuje kroky potřebné pro odstranění RAID polí, nastavení a instalaci operačního systému na SSD disk v zachranném režimu Hetzner a konfiguraci disků sda a sdb pro ukládání dat a zálohy.

### Obsah

- [Zachranný Režim Hetzner](#zachranný-režim-hetzner)
- [Odstranění RAID Polí](#odstranění-raid-polí)
- [Nastavení SSD pro Systém](#nastavení-ssd-pro-systém)
- [Formátování Disků](#formátování-disků)
- [Montování Disků](#montování-disků)
- [Automatizace Montování při Startu](#automatizace-montování-při-startu)



## Zachranný Režim Hetzner

Instalace operačního systému probíhá v zachranném režimu Hetzner, což umožňuje přímý přístup k diskům a systémovým nástrojům bez spuštění hlavního operačního systému.

## Odstranění RAID Polí

Pro odstranění existujících RAID polí použijte nástroj mdadm. Příkazem mdadm --stop zastavíte RAID pole, kde X je číslo RAID pole.
Krok za krokem:

Pro každé RAID pole:

1. Zastavte RAID pole:

```bash
mdadm --stop /dev/mdX
```

Nahraďte X skutečným číslem RAID pole.

2. Odstraňte RAID konfiguraci z disku:

## Nastavení SSD pro Systém

SSD disk `(nvme0n1)` byl použit pro instalaci operačního systému Ubuntu 22.04. Níže jsou uvedeny kroky pro jeho přípravu a instalaci:

1. Zálohuj důležitá data: Než začneš jakékoliv úpravy na disku, ujisti se, že máš zálohy všech důležitých dat.

2. Příprava SSD pro instalaci systému:

- Spusť fdisk /dev/nvme0n1 a smaž všechny existující oddíly:

```bash
d
```
a poté vytvoř nový oddíl:

```bash
n
w
```

3. Formátování a připojení nového oddílu

- Formátuj oddíl:

```bash
mkfs.ext4 /dev/nvme0n1p1
```

- Připoj oddíl:

```bash
mount /dev/nvme0n1p1 /mnt
```

## Formátování Disků

### Disk pro Data (sda)

1. Smazání existujících oddílů a vytvoření nového:

```bash
fdisk /dev/sda
```

- `d` pro smazání oddílů
- `n` pro nový oddíl
- `w` pro uložení změn

2. Formátujte nový oddíl:

```bash
mkfs.ext4 /dev/sda1
```

## Disk pro Zálohy (sdb)

Opakujte kroky formátování jak je popsáno u `sda`.

## Montování Disků

1. Vytvořte bodek připojení a připojte disky:

- Pro data:

```bash
mkdir -p /mnt/data
mount /dev/sda1 /mnt/data
```

2. Pro zálohy:

```bash
mkdir -p /mnt/backup
mount /dev/sdb1 /mnt/backup
```

## Automatické přiojení disků při startu systému

- Přidejte následující řádky do souboru `/etc/fstab` pro automatické montování disků při startu:

```bash
/dev/sda1 /mnt/data ext4 defaults 0 2
/dev/sdb1 /mnt/backup ext4 defaults 0 2
```

