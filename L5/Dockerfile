# syntax=docker/dockerfile:1.2

# ETAP 1: Klonowanie repozytorium
FROM alpine/git AS clone_stage
WORKDIR /app

# Dodanie GitHuba do znanych hostów (żeby proces nie zawiesił się na pytaniu (yes/no))
RUN mkdir -p -m 0600 ~/.ssh && ssh-keyscan github.com >> ~/.ssh/known_hosts

# Pobranie kodu przez protokół SSH z wykorzystaniem sekretu
RUN --mount=type=ssh git clone git@github.com:JakubGwo/pawcho6.git .

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


# ETAP 2: # Obraz bazowy dla drugiego etapu
FROM nginx:alpine

# Etykieta łącząca obraz z paczką na GitHubie (Dobra praktyka z wykładu)
LABEL org.opencontainers.image.source=https://github.com/JakubGwo/pawcho6

# Intalacja curl do sprawdzenia stanu zdrowia kontenera i czyszcenie pamięci podręcznej apk
RUN apk add --update curl && rm -rf /var/cache/apk/*

# Skopiowanie wygenerowanego pliku HTML z etapu 1 do katalogu Nginxa
COPY --from=clone_stage /app/create_app.sh /usr/share/nginx/html/create_app.sh


# HEALTHCHECK będzie sprawdzać dostępność aplikacji co 30 sekund, z czasem oczekiwania 1 sekundy i okresem startowym 5 sekund
HEALTHCHECK --interval=30s --timeout=1s --start-period=5s \
    CMD curl -f http://localhost/ || exit 1

# Otwarcie portu 80, na którym działa nginx
EXPOSE 80