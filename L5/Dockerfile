#STAGE 1
# Utworzenie pustego konetenera i dnadanie mu nazwy
FROM scratch AS builder

# Dodanie systemu będącego bazą dla kontenera
ADD alpine-minirootfs-3.23.3-x86_64.tar /

# Utworzenie katalogu roboczego
WORKDIR /app

# Dodanie argumentu wersji aplikacji
ARG VERSION="1.0"

# Utworzenie skryptu tworzącego aplikację HTML
RUN echo '#!/bin/sh' > create_app.sh && \
    echo 'echo "<!DOCTYPE html><html lang=\"pl-PL\"><head><meta charset=\"UTF-8\"><title>PAwChO lab 5 Gwozdowski</title></head><body>" > /usr/share/nginx/html/index.html' >> create_app.sh && \
    echo 'echo "<h1>Lab 5</h1>" >> /usr/share/nginx/html/index.html' >> create_app.sh && \
    echo 'echo "<p>Wersja aplikacji: '${VERSION}'</p>" >> /usr/share/nginx/html/index.html' >> create_app.sh && \
    echo 'echo "<p>Nazwa serwera: $(hostname)</p>" >> /usr/share/nginx/html/index.html' >> create_app.sh && \
    echo 'echo "<p>Adres IP serwera: $(hostname -i)</p>" >> /usr/share/nginx/html/index.html' >> create_app.sh && \
    echo 'echo "<p>Autor: Jakub Gwozdowski</p>" >> /usr/share/nginx/html/index.html' >> create_app.sh && \
    echo 'echo "</body></html>" >> /usr/share/nginx/html/index.html' >> create_app.sh && \
    chmod +x create_app.sh


#STAGE 2

# Obraz bazowy dla drugiego etapu
FROM nginx:alpine

# Dodanie argumentu wersji i ustawienie go jako zmiennej 
ARG VERSION
ENV APP_VERSION=$VERSION

# Intalacja curl do sprawdzenia stanu zdrowia kontenera i czyszcenie pamięci podręcznej apk
RUN apk add --update curl && \
    rm -rf /var/cache/apk/*

# Skopiowanie skryptu tworzącego aplikację HTML do katalogu entrypoint.d, aby został wykonany przy starcie kontenera
COPY --from=builder /app/create_app.sh /docker-entrypoint.d/40-create_app.sh

# HEALTHCHECK będzie sprawdzać dostępność aplikacji co 30 sekund, z czasem oczekiwania 1 sekundy i okresem startowym 5 sekund
HEALTHCHECK --interval=30s --timeout=1s --start-period=5s \
    CMD curl -f http://localhost/ || exit 1

# Otwarcie portu 80, na którym działa nginx
EXPOSE 80

