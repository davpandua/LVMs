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
    Llista els volums físics creats.  
 **VG** *(Volume Group)*:  
  Crea grups per poder gestionar els volums lògics que pertanyin al grup corresponent. D'aquesta manera es poden organitzar d'una manera més eficient.  
  **vgcreate multimedia**  
  Crea un grup lògic anomenat *multimedia*
