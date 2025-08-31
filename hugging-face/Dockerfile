FROM ubuntu:22.04 AS builder

WORKDIR /app

RUN apt-get update; \
    apt-get install -y wget unzip; \
    wget https://github.com/XTLS/Xray-core/releases/latest/download/Xray-linux-64.zip; \
    unzip Xray-linux-64.zip; \
    rm -f Xray-linux-64.zip; \
    mv xray xy; \
    wget -O cd https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64; \
    chmod +x cd; \
    wget -O td https://github.com/tsl0922/ttyd/releases/latest/download/ttyd.x86_64; \
    chmod +x td; \
    wget -O supercronic https://github.com/aptible/supercronic/releases/latest/download/supercronic-linux-amd64; \
    chmod +x supercronic

############################################################

FROM ubuntu:22.04

LABEL org.opencontainers.image.source="https://github.com/Amosgantian/one-nodeOnhugingface"

ENV TZ=Asia/Shanghai \
    UUID=723b23a3-c247-43c9-8c0d-ba3a77b74e16 \
    DOMAIN=amsgs-rxhp.hf.space

COPY entrypoint.sh /entrypoint.sh
COPY app /app

RUN export DEBIAN_FRONTEND=noninteractive; \
    apt-get update; \
    apt-get install -y tzdata openssh-server curl ca-certificates wget vim net-tools supervisor unzip iputils-ping telnet git iproute2 --no-install-recommends; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*; \
    chmod +x /entrypoint.sh; \
    chmod -R 777 /app; \
    useradd -u 1000 -g 0 -m -s /bin/bash user; \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime; \
    echo $TZ > /etc/timezone

COPY --from=builder /app/xy /usr/local/bin/xy
COPY --from=builder /app/cd /usr/local/bin/cd
COPY --from=builder /app/td /usr/local/bin/td
COPY --from=builder /app/supercronic /usr/local/bin/supercronic

EXPOSE 7860

ENTRYPOINT ["/entrypoint.sh"]
CMD ["supervisord", "-c", "/app/supervisor/supervisord.conf"]
CMD ["xy", "-config", "/app/xy/config.json"]
CMD ["cd", "tunnel", "--no-autoupdate", "--edge-ip-version", "auto", "--protocol", "http2", "run", "--token","eyJhIjoiZTZhZTliNTBjZDNkZjJkZGEyY2FhZjg2N2FlMWQ1ZWYiLCJ0IjoiNjI4MWQ5MjEtMjZmZi00M2JhLWExODMtNTU3OTA1Yzk3NTNiIiwicyI6Ik1qZzRZVGxsWW1NdFl6WTFOQzAwTXpVMUxXSXlOelF0Tm1SbE0yVXlNRE14WkRVMyJ9"]
