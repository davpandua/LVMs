# LVMs
## Gestió de volums lògics
#### Exemples en màquines virtuals (**VM**)  

* Què son:  
Son discos i/o particions gestionades de tal manera que podem agrupar-les per augmentar o reduir el seu tamany de manera flexible com a una alternativa a les limitacions que comporten el clàssic particionament (MBR/msdos).  
* Definicions i exemples de:  
**PV** *(Phisycal Volume)*:  
  Son els volums (discos) físics (reals) que s'utilitzaràn per crear els grups lògics (**VG**) i els volums lògics (**LV**).  
  Comandes bàsiques per als volums físics:  

  **pvcreate /dev/vda**  
    Agafa el disc /dev/vda i el marca com a volum físic per a la gestió de **LVMs**.  
  **pvs**  
    Llista els volums físics creats:  
```
    [root@localhost ~]# pvs  
    PV         VG     Fmt  Attr PSize   PFree  
    /dev/sda2  fedora lvm2 a--   19.00g      0  
    /dev/vda          lvm2 ---  204.80m 204.80m  
    /dev/vdb          lvm2 ---  204.80m 204.80m  
```
  **VG** *(Volume Group)*:  
  Crea grups per poder gestionar els volums lògics que pertanyin al grup corresponent. D'aquesta manera es poden organitzar d'una manera més eficient.  
  **vgcreate multimedia /dev/vda**  
  Crea un grup lògic anomenat *multimedia* en el disc **vda** (previament creat com a volum físic, **pv**).  
  Aquest grup servirà per poder-lo ampliar o reduir o afegir-hi més volums lògics.  
  **vgs**  
  Llistem els grups lògics:  
```
  [root@localhost ~]# vgs  
  VG         #PV #LV #SN Attr   VSize   VFree  
  fedora       1   2   0 wz--n-  19.00g      0  
  multimedia   1   0   0 wz--n- 200.00m 200.00m  
  ```
  Veiem el grup lògic *multimedia* creat amb 200MB d'espai.  
  **LV** *(Logical Volume)*  
  Crea els volums lògics dintre dels grups lògics corresponents. Aquests LV's tindràn el nom que li indiquem, per exemple, dintre del grup *multimedia* creem un LV de 100MB anomenat *musica*:  
**lvcreate -L 100M -n musica multimedia**  
```
[root@localhost ~]# lvcreate -L 100M -n musica multimedia  
Logical volume "musica" created.
```
Comprovem els LV's:  
**lvs**  
```
[root@localhost ~]# lvs  
LV     VG         Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert  
  root   fedora     -wi-ao----  17.00g  
  swap   fedora     -wi-ao----   2.00g  
  musica multimedia -wi-a----- 100.00m
```
Veiem el volum lògic *musica* que forma part del grup *multimedia*.  

#### Entorn de pràtiques
Seguint la mecànica dels exemples anteriors, ens cal donar un sistema d'arxius al volum lògic per tal de poder emmagatzemar informació:  
**mkfs.ext4 /dev/multimedia/musica**  
```
[root@localhost ~]# mkfs.ext4 /dev/multimedia/musica  
mke2fs 1.43.1 (08-Jun-2016)  
Creating filesystem with 102400 1k blocks and 25688 inodes  
Filesystem UUID: b77ec95c-80de-40ac-a133-ce76c264d554  
Superblock backups stored on blocks:  
 8193, 24577, 40961, 57345, 73729  
 
Allocating group tables: done  
Writing inode tables: done  
Creating journal (4096 blocks): done  
Writing superblocks and filesystem accounting information: done
```
Montem el dispositiu per poder accedir a ell:  
**mount /dev/multimedia/musica /mnt**  
Comprovem:  
**df -h**  
```
[root@localhost ~]# df -h
Filesystem                     Size  Used Avail Use% Mounted on
devtmpfs                       485M     0  485M   0% /dev
tmpfs                          497M  344K  496M   1% /dev/shm
tmpfs                          497M  1.3M  496M   1% /run
tmpfs                          497M     0  497M   0% /sys/fs/cgroup
/dev/mapper/fedora-root         17G  5.1G   11G  33% /
tmpfs                          497M  148K  497M   1% /tmp
/dev/sda1                      976M  114M  796M  13% /boot
tmpfs                          100M   28K  100M   1% /run/user/42
tmpfs                          100M  4.6M   95M   5% /run/user/1000
/dev/mapper/multimedia-musica   93M  1.6M   85M   2% /mnt
```
Veiem, a la última linia, que s'ha montat correctament.  
* **NOTA:** La tasca demana que el **VG** es digui **practica1** i, com que no es diu així, hem disposo a cambiarli el nom:  
**vgrename multimedia practica1**  
```
vda                 252:0    0 204.8M  0 disk 
└─multimedia-musica 253:2    0   100M  0 lvm  /mnt
[root@localhost ~]# vgrename multimedia practica1
  Volume group "multimedia" successfully renamed to "practica1"
```
```
vda                252:0    0 204.8M  0 disk 
└─practica1-musica 253:2    0   100M  0 lvm  /mnt
```
El mateix m'ha passat amb el volum lògic:  
**lvrename practica1 musica dades**  
```
vda                252:0    0 204.8M  0 disk 
└─practica1-musica 253:2    0   100M  0 lvm  /mnt
[root@localhost ~]# lvrename practica1 musica dades
  Renamed "musica" to "dades" in volume group "practica1"
```
```
vda               252:0    0 204.8M  0 disk 
└─practica1-dades 253:2    0   100M  0 lvm  /mnt
```
* El volum lògic **dades** ha de ocupar tot el grup lògic. Hem disposo a expandir el volum lògic al màxim disponible, **200M**:  
**lventend -l +100%FREE /dev/practica1/dades**  
```
[root@localhost ~]# lvextend -l +100%FREE /dev/practica1/dades
  Size of logical volume practica1/dades changed from 100.00 MiB (25 extents) to 200.00 MiB (50 extents).
  Logical volume practica1/dades successfully resized.
```
```
vda               252:0    0 204.8M  0 disk 
└─practica1-dades 253:2    0   200M  0 lvm  /mnt
```
* El sistema d'arxius del volum lògic ha de ser **xfs**. Hem disposo ha cambiar-li el sistema d'arxius.  
* Primer haig de desmontar **/mnt:**  
**umount /mnt**  
* Ja que ja existeix un sistema d'arxius, s'ha de forçar amb **-f**:  
**mkfs.xfs -f /dev/practica1/dades**  
