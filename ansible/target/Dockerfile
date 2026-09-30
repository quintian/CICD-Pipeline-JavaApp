FROM eclipse-temurin:17-jre

RUN apt-get update \
    && apt-get install -y --no-install-recommends openssh-server python3 procps \
    && rm -rf /var/lib/apt/lists/* \
    && useradd -m -s /bin/bash appuser \
    && echo "appuser:appuser" | chpasswd \
    && mkdir -p /run/sshd /opt/petclinic \
    && chown -R appuser:appuser /opt/petclinic \
    && sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config \
    && sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

EXPOSE 22 8080

CMD ["/usr/sbin/sshd", "-D", "-e"]
