# Proxmox - Augmentation de l'espace de stockage local

## Contexte
Ce guide décrit comment augmenter l'espace de stockage local sur un serveur **Proxmox VE** en redimensionnant un volume logique LVM.

## ✅ **Étapes réalisées**

### 1. Vérification des volumes existants
```bash
pvs
```
**Résultat :**
```
PV         VG        Fmt  Attr PSize    PFree 
/dev/sda3  pve       lvm2 a--  <464.76g 16.00g
/dev/sdd1  vm-docker lvm2 a--    <3.64t <2.03t
```

```bash
lvs
```
**Résultat :**
```
LV                  VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
root                pve       -wi-ao----  96.00g 
swap                pve       -wi-ao----   8.00g 
data                pve       twi-aotz-- 337.86g             0.00   0.50 
```

### 2. Suppression du volume `data`

```bash
lvremove /dev/pve/data
```
**Validation :**
```
Do you really want to remove active logical volume pve/data? [y/n]: y
Logical volume "data" successfully removed.
```

### 3. Vérification après suppression
```bash
pvs
```
```
PV         VG        Fmt  Attr PSize    PFree   
/dev/sda3  pve       lvm2 a--  <464.76g <360.76g
/dev/sdd1  vm-docker lvm2 a--    <3.64t   <2.03t
```

### 4. Extension du volume `root`
```bash
lvresize -L +154G /dev/pve/root
```
**Résultat :**
```
Size of logical volume pve/root changed from 96.00 GiB to 250.00 GiB.
Logical volume pve/root successfully resized.
```

### 5. Redimensionnement du système de fichiers
```bash
resize2fs /dev/pve/root
```
**Résultat :**
```
Filesystem at /dev/pve/root is mounted on /; on-line resizing required
The filesystem on /dev/pve/root is now 65536000 (4k) blocks long.
```

### 6. Vérification finale
```bash
df -h /
lsblk
```

## ✅ **Résultat final**
Le volume `root` est maintenant **250G** et Proxmox dispose de plus d’espace pour le stockage.

---

🎉 **Opération réussie !**
