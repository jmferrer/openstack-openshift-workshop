# openstack-openshift-workshop
## OpenStack

### Que es
Es un producto que te permite exponer tu infraestructura como servicio.

Por ejemplo, nos permite hacer la misma funcionalidad de cualquier sistema de virtualización. :-D

### Despliegue
Ejecutar ansible de https://github.com/paradigmadigital/ansible-openstack-vcenter con keystone+databases+dashboard .

Instalación desactualizada:
* Instalar paquetes necesarios:
```
apt-get install qemu-kvm qemu-system bridge-utils virt-manager
```
* Descargar y descomprimir appliance:
```
/etc/init.d/libvirt-bin stop
cd /var/lib/libvirt/images
wget http://elmanytas.es/filesblog/informatica/virtualizacion/20161102-OpenStack/openstack-vcenter.tgz
tar -zxvf openstack-vcenter.tgz
cp etc/libvirt/qemu/openstack-vcenter.xml /etc/libvirt/qemu/
service libvirt-bin start
```

* Arrancar el appliance con virt-manager y entrar con openstack/openstack

* Instalar openssh
```
apt-get -y install openssh-server
```

* Crear claves y distribuirlas al usuario root de todos los nodos de computación
```
ssh-keygen
```

* Comprobar que los hosts en /home/openstack/ansible-openstack-vcenter/etc_ansible/hosts están bien, cambiar global_vars.yml y:
```
ansible-playbook -i hosts site.yml
```

No es mejor en general. Es mejor para mi porque me sirve para crear entornos de desarrollo en cualquier sitio por barato que sea.

[esquema arquitectura]

### Alternativas de despliegue
RedHat: RDO
Ubuntu: MAAS
Mirantis
HP: Helium
RackSpace: Private Cloud
Custom:
* ansible
* puppet
* chef
* manual
Otras que no conozco.

¡Cuidado con los impostores!

### Que es realmente
#### ¡No es un sistema de virtualización!
Puede sustituir a nuestro sistema de virtualización pero ¡cambiarlo no tiene porqué ser motivo suficiente para usar OpenStack!

Permite:
* agilizar nuestro proceso de desarrollo
* saber cuanto gasta cada proyecto
* extremadamente síncrono
* no expuesto a fallos humanos
* escalable en tiempo y coste razonables
* abstracción de servicios
* automatización
* ...
* be cloud!

#### Infraestructura como servicio
Servicios de infraestructura:
* identidad (keystone) (ejemplo de gestión de ldap)
* dns (designate) (ejemplo de gestión de DNS multisitio)
* almacenamiento compartido (manila) (ejemplo de gestión de cabinas de disco)
* archivos (swift)
* bases de datos (trove)
* vpn (neutron)
* orquestación (heat)
* redes (neutron)
* servidores (ironic)
* imágenes (glance)
* dispositivos de bloques (cinder)
* colas (zaqar)
* procesamiento intensivo de datos (sahara)
* …
* y también máquinas virtuales (nova)

¡Cuidado con los impostores!

### Despliegue
Esquema de aplicación (drupal):
* aplicación
* persistencia
* base de datos

[esquema]

#### Despliegue de infraestructura
##### Volúmenes persistentes
Deberíamos usar manila pero no lo tengo desplegado así que vamos a usar un servidor de NFS.

Ejecutamos:
```
heat stack-create test --template-file=nfs-server.yaml
```

##### Base de datos
Deberíamos usar trove pero no lo tengo desplegado así que vamos a usar un cluster de Percona con un balanceador tcp al 3306.

Ejecutamos cluster-percona.yaml .

#### Despliegue de aplicación
Tres servidores con Drupal pasando como parámetros cluster Percona, volumen para /etc/drupal, /var/lib/drupal/files y red pública con un balanceador tcp al 80.

Ejecutamos drupal.yaml .

### Si es tan bueno ¿porque nadie lo usa?

#### Inercia
##### Tenemos
Tenemos unos procedimientos que están adaptados a una infraestructura no cloud.
Tratamos a nuestros componentes como mascotas. Sabemos de que hay que hacer backup, que hay que monitorizar, que tienen instalado, ...

##### Problemas
¿Pero como gestionamos todas estas mascotas? ¿Contratamos a un ejército de personas que les pongan nombre, las instalen, decidan de que hacer backup, que monitorizar, ...?

##### Solución
Si las tratamos como un rebaño solo hará falta un pastor

#### Supuesta complejidad
##### Complejidad de la infraestructura hardware
Tanto de servidores como de red.

##### Complejidad de la infraestructura software
Tiene muchas piezas y si falla una sola todo se cae.

Su orientación a una filosofía de servicios puede hacer difícil trazar un error si no se conoce la arquitectura.

##### O te pegas o pagas
Acabo de demostrar que una sola persona con un poco de tiempo puede desarrollar una distribución de OpenStack pero si no quieres pagar a alguien en tu empresa ¡paga a alguien fuera de ella!

Hay multitud de empresas que ofrecen servicios alrededor de OpenStack.

##### Porqué está mal hecho
Falta gente.

#### Vacas sagradas
##### Desconocimiento
¿Porque cambiar un sistema de virtualización por otro si el que tenemos funciona?
¡No es un sistema de virtualización!

## OpenShift

### Posible instalación con Heat
RedHat trae una plantilla de instalación oficial pero yo me he hecho la mía.

### Despliegue de aplicación
### Explicación de arquitectura
### Explicación de la pila
