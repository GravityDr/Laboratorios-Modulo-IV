# Laboratorios-Modulo-IV
# Instalar Apache
sudo dnf install httpd -y

# Crear website en puerto 80
sudo mkdir /var/www/holamundo
echo "<html><body><h1>Hola Mundo</h1></body></html>" | sudo tee /var/www/holamundo/index.html

# Configurar virtual host 80
echo '<VirtualHost *:80>
    DocumentRoot /var/www/holamundo
    ServerName localhost
    <Directory /var/www/holamundo>
        Require all granted
    </Directory>
</VirtualHost>' | sudo tee /etc/httpd/conf.d/holamundo.conf

# Crear website en puerto 8080
sudo mkdir /var/www/datos
echo "<html><body><h1>Nombre: Tu Nombre</h1><h1>Matrícula: Tu Matrícula</h1><h1>Materia: Tu Materia</h1></body></html>" | sudo tee /var/www/datos/index.html

# Configurar virtual host 8080
echo 'Listen 8080
<VirtualHost *:8080>
    DocumentRoot /var/www/datos
    ServerName localhost
    <Directory /var/www/datos>
        Require all granted
    </Directory>
</VirtualHost>' | sudo tee /etc/httpd/conf.d/datos.conf

# Ajustar firewall y reiniciar
sudo firewall-cmd --permanent --add-service=http --add-port=8080/tcp
sudo firewall-cmd --reload
sudo systemctl enable --now httpd
sudo systemctl restart httpd

# Verificar
curl localhost && curl localhost:8080


/////////////////////////////////////////////////////////////////////////////////////////////



# Instalar Postfix y herramientas
sudo dnf install postfix mailx -y

# Configurar Postfix
sudo sed -i 's/inet_interfaces = localhost/inet_interfaces = all/' /etc/postfix/main.cf
sudo systemctl enable --now postfix

# Enviar correo de prueba (REEMPLAZA TUS DATOS)
echo "Cuerpo del mensaje: Hamlet alcantara - 2019-8514" | mail -s "MambruSeFueALaGuerra" os3conadrian@gmail.com

# Ver cola de correos
mailq

# Asegurar firewall permite SMTP
sudo firewall-cmd --permanent --add-service=smtp
sudo firewall-cmd --reload

////////////////////////////////////////////////////////////////////////////////////////////



# Instalar CUPS y PDF printer
sudo dnf install cups cups-pdf -y

# Configurar CUPS
sudo systemctl enable --now cups
sudo firewall-cmd --permanent --add-service=ipp
sudo firewall-cmd --reload

# Agregar impresora PDF
sudo lpadmin -p PDF_Printer -E -v cups-pdf:/ -m drv:///cups-pdf.ppd.gz

# Configurar acceso desde cliente
sudo sed -i 's/Listen localhost:631/Listen 0.0.0.0:631/' /etc/cups/cupsd.conf
sudo systemctl restart cups

# Desde CLIENTE (ejecutar en PC cliente):
# 1. Abrir navegador en: http://IP-del-servidor:631
# 2. Agregar impresora usando la opción "IPP"
# 3. Usar dirección: ipp://IP-del-servidor/printers/PDF_Printer

# Probar impresión desde servidor
echo "Prueba de impresión" | lp -d PDF_Printerclear



sudo systemctl enable postfix
