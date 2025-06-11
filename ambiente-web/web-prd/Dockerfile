# Use a imagem oficial do Apache HTTP Server como base
FROM httpd:2.4

# Define o diretório de trabalho dentro do container (opcional, mas boa prática)
WORKDIR /usr/local/apache2/htdocs/

# Copie seus arquivos do site aqui (se tiver algum)
# Por exemplo, se você tem um index.html no mesmo diretório do seu Dockerfile:
COPY ./html .

# Altera a porta de escuta do Apache para 8080 diretamente no httpd.conf
RUN sed -i 's/^Listen 80/Listen 8080/' /usr/local/apache2/conf/httpd.conf

# Expõe a porta 8080. Isso é para documentação.
EXPOSE 8080

# O comando padrão do Apache já inicia o servidor, não precisa especificar CMD
CMD ["httpd-foreground"]