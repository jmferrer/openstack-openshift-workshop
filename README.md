# openstack-openshift-workshop
## OpenStack

### Que es
Es un producto que te permite exponer tu infraestructura como servicio.

Por ejemplo, nos permite hacer la misma funcionalidad de cualquier sistema de virtualización. :-D

### Despliegue
#### ¿Que queremos?
Se pretende hacer un despliegue:
* rápido, sencillo
* en cualquier tipo de hardware
* sin importar su ubicación física
* utilizando un appliance

#### ¿Que necesitamos?
Necesitamos una sola máquina con una sola tarjeta de red. Esa máquina puede ser desde un servidor dedicado barato ( 30€/mes ) en algún lugar de internet hasta un servidor en el propio CPD.

#### ¿Como lo hacemos?
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

[esquema vcenter-network.dia]

### Alternativas de despliegue
RedHat: RDO
Ubuntu: MAAS
RackSpace: Private Cloud
+Mirantis
HP: Helium
Custom:
* ansible
* puppet
* chef
* manual
Otras que no conozco.

¡NO MUERDE!

### Que es realmente
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

#### ¡No es un sistema de virtualización!
Puede sustituir a nuestro sistema de virtualización pero ¡cambiarlo no tiene porqué ser motivo suficiente para usar OpenStack!

Permite:
* agilizar nuestro proceso de desarrollo
* saber cuanto gasta cada proyecto
* extremadamente síncrono
* no expuesto a fallos humanos imprevisibles
* abstracción de servicios
* automatización
* ...
* be cloud!

### Despliegue
Esquema de aplicación (drupal):
* aplicación
* persistencia
* base de datos

[esquema drupal-openstack.dia]

#### Despliegue de infraestructura
##### Volúmenes persistentes
Deberíamos usar manila pero no lo tengo desplegado así que vamos a usar un servidor de NFS.

Ejecutamos:
```
heat stack-create test --template-file=nfs-server.yaml
```

[esquema persistencia-openstack.dia]

##### Base de datos
Deberíamos usar trove pero no lo tengo desplegado así que vamos a usar un cluster de Percona con un balanceador tcp al 3306.

Ejecutamos cluster-percona.yaml .

[esquema database-openstack.dia]

#### Despliegue de aplicación
Tres servidores con Drupal pasando como parámetros cluster Percona, volumen para /etc/drupal, /var/www.html/drupal/sites/default y red pública con un balanceador tcp al 80.

Ejecutamos drupal.yaml .

[esquema drupal-app-openstack.dia]

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

#### ¡Oportunidades personales!
Hay mucho trabajo por hacer.
Trove:
* mysql
* mongodb
* percona

¿Y el resto?

## OpenShift

### Posible instalación con Heat
RedHat trae una plantilla de instalación oficial pero yo me he hecho la mía.
¡No da tiempo! -> Usaremos un OpenShift ya instalado.

### Despliegue de aplicación
```
oc create -f drupal-openshift-pv.yaml
oc new-app -f drupal-openshift-persistent-template.yaml -p DRUPAL_HOSTNAME=drupal.jmferrer.os.paradigmadigital.com
```
Cambiar DNS a 192.168.42.174 .

### Vacas sagradas
¿Porque cambiar un sistema de virtualización por otro si el que tenemos funciona?
¡PORQUE OPENSTACK NO ES UN SISTEMA DE VIRTUALIZACIÓN!

### ¡El mileniarismo va a llegaaaarrrrrr!
Tecnología disruptiva hace que las personas que no sepan manejarla se queden fuera del mercado laboral.

### OpenShift ¿por donde van las comunicaciones?
* router
* service
* replication controller
* pod
* container

### Explicación de la pila
* linux
* contenedores, cgroups, ...
* docker
* kubernetes
* OpenShift
