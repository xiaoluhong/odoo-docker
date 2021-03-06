FROM debian:stretch

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            node-less \
            python3-pip \
            python3-setuptools \
            python3-renderpm \
            libssl1.0-dev \
            xz-utils \
            python3-watchdog \
        && curl -o wkhtmltox.deb -SL  https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/$(curl -s https://api.github.com/repos/wkhtmltopdf/wkhtmltopdf/releases/latest | grep tag_name | cut -d '"' -f 4)/wkhtmltox_$(curl -s https://api.github.com/repos/wkhtmltopdf/wkhtmltopdf/releases/latest | grep tag_name | cut -d '"' -f 4)-1.stretch_amd64.deb  \
        && dpkg --force-depends -i wkhtmltox.deb

# Install Odoo
ENV ODOO_VERSION 11.0
ENV ODOO_RELEASE latest
RUN set -x; \
        curl -o odoo.deb -SL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && dpkg --force-depends -i odoo.deb \
        && apt-get update \
        && apt-get -y install -f --no-install-recommends \
        && pip3 install num2words xlwt  phonenumbers\
        && rm -rf /var/lib/apt/lists/* odoo.deb

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/
RUN chown odoo /etc/odoo/odoo.conf

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN     mkdir -p /var/lib/odoo/extra-addons/     \
    &&  mkdir -p /var/lib/odoo/data/             \
    &&  chown -R odoo /var/lib/odoo/             \
    &&  chmod +x /entrypoint.sh
        
VOLUME ["/var/lib/odoo"]

# Expose Odoo services
EXPOSE 8069 8071

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
