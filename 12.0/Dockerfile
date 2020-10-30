FROM python:3.7-buster

SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            dirmngr \
            fonts-noto-cjk \
            gnupg \
            node-less \
            python3-num2words \
            python3-pip \
            python3-phonenumbers \
            python3-pyldap \
            libldap2-dev \
            libsasl2-dev \
            python3-qrcode \
            python3-renderpm \
            python3-setuptools \
            python3-slugify \
            python3-vobject \
            python3-watchdog \
            python3-xlrd \
            python3-xlwt \
            python3-venv \
            xz-utils

RUN curl -o wkhtmltox.deb -sSL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.buster_amd64.deb\
        && apt-get install -y --no-install-recommends ./wkhtmltox.deb \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# Install rtlcss
RUN echo "deb http://deb.nodesource.com/node_8.x buster main" > /etc/apt/sources.list.d/nodesource.list \
    && GNUPGHOME="$(mktemp -d)" \
    && export GNUPGHOME \
    && repokey='9FD3B784BC1C6FC31A8A0A1C1655A0AB68576280' \
    && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "${repokey}" \
    && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/nodejs.gpg.asc \
    && gpgconf --kill all \
    && rm -rf "$GNUPGHOME" \
    && apt-get update \
    && apt-get install --no-install-recommends -y nodejs npm \
    && npm install -g rtlcss \
    && rm -rf /var/lib/apt/lists/*

# Install Odoo
RUN apt-get update && apt-get -y install libpq-dev python3-dev build-essential python3-psycopg2
RUN pip3 install http://nightly.odoo.com/12.0/nightly/src/odoo_12.0.latest.tar.gz
RUN pip3 uninstall -y Wekzeug && pip3 install Werkzeug==0.11.15
RUN pip3 install --pre odoo12-addon-l10n-it-abicab  \
            odoo12-addon-l10n-it-ddt \
            odoo12-addon-l10n-it-account \
            odoo12-addon-l10n-it-ipa \
            odoo12-addon-l10n-it-corrispettivi \
            odoo12-addon-l10n-it-rea \
            odoo12-addon-l10n-it-fatturapa \
            odoo12-addon-l10n-it-fiscalcode \
            odoo12-addon-l10n-it-intrastat \
            odoo12-addon-l10n-it-ateco \
            odoo12-addon-l10n-it-pec \
            odoo12-addon-l10n-it-location-nuts \
            odoo12-addon-l10n-it-fiscalcode-crm \
            odoo12-addon-l10n-it-fatturapa-pec \
            odoo12-addon-l10n-it-causali-pagamento \
            odoo12-addon-l10n-it-codici-carica \
            odoo12-addon-l10n-it-sdi-channel \
            odoo12-addon-l10n-it-account-stamp \
            odoo12-addon-l10n-it-fatturapa-in \
            odoo12-addon-l10n-it-esigibilita-iva \
            odoo12-addon-l10n-it-delivery-note \
            odoo12-addon-l10n-it-pos-fatturapa \
            odoo12-addon-l10n-it-intrastat-statement \
            odoo12-addon-l10n-it-fatturapa-sale \
            odoo12-addon-l10n-it-fatturapa-out \
            odoo12-addon-l10n-it-corrispettivi-sale \
            odoo12-addon-l10n-it-dichiarazione-intento \
            odoo12-addon-l10n-it-ricevute-bancarie \
            odoo12-addon-l10n-it-vat-registries \
            odoo12-addon-l10n-it-fiscalcode-sale \
            odoo12-addon-l10n-it-withholding-tax \
            odoo12-addon-l10n-it-pos-fiscalcode \
            odoo12-addon-l10n-it-reverse-charge \
            odoo12-addon-l10n-it-split-payment \
            odoo12-addon-l10n-it-central-journal

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/

VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071 8072

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

COPY wait-for-psql.py /usr/local/bin/wait-for-psql.py

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
