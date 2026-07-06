# Obtener privilegios de administrador
sudo su

# Actualizar repositorios
apt update

# Instalar editor de texto
apt install gedit -y

# Instalar dependencias necesarias
apt install autoconf automake libtool pkg-config \
libgtk2.0-dev libnet1-dev libpcap-dev flex bison -y

# Ir a la carpeta donde se descargarÃ¡ el proyecto
cd /opt

# Clonar el repositorio oficial
git clone https://github.com/tomac/yersinia.git

# Entrar al proyecto
cd yersinia

# Generar los archivos de configuraciÃ³n
./autogen.sh

# Configurar el proyecto
./configure

# Entrar a la carpeta donde se corregirÃ¡n los errores
cd src

# Abrir el primer archivo
gedit yersinia.c

De:
posix_signal(int signo, void (*handler)()) 
por:
posix_signal(int signo, void (*handler)(int)) 


# (Guardar y cerrar despuÃ©s de modificar)

# Abrir el segundo archivo
gedit admin.c

lÃ­neas 339, 349, 380 y 387
De:
â€¢	admin_th_network_peer_exit(term_node, sock);
por:
â€¢	admin_th_network_peer_exit(term_node, *(int *)sock);

TambiÃ©n se cambiÃ³:
â€¢	fail = term_add_node(&term_node, TERM_VTY, sock, pthread_self());
por:
â€¢	fail = term_add_node(&term_node, TERM_VTY, *(int *)sock, pthread_self());


# (Guardar y cerrar despuÃ©s de modificar)

# Regresar a la carpeta principal del proyecto
cd ..

# Limpiar compilaciones anteriores (opcional pero recomendado)
make clean

# Compilar
make

# Instalar
make install


# Ejecutar la interfaz grÃ¡fica
yersinia -G
