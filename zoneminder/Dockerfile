FROM ubuntu:xenial
MAINTAINER Joachim Wickman <joachim@wickman.in>

RUN apt-get update -y -qq && \
    apt-get install software-properties-common -y && \
    add-apt-repository ppa:iconnor/zoneminder && \
    apt-get update -y -qq && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    nano \
    apache2 \
    build-essential \
    cmake \
    dh-autoreconf \
    dpatch \
    git \
    libapache2-mod-php \
    libarchive-zip-perl \
    libavcodec-dev \
    libavdevice-dev \
    libavfilter-dev \
    libavformat-dev \
    libavresample-dev \
    libav-tools \
    libavutil-dev \
    libbz2-dev \
    libcurl4-gnutls-dev \
    libdate-manip-perl \
    libdbd-mysql-perl \
    libdbi-perl \
    libdevice-serialport-perl \
    libgcrypt-dev \
    libgnutls-openssl-dev \
    libjpeg-turbo8 \
    libjpeg-turbo8-dev \
    libmime-lite-perl \
    libmime-perl \
    libmp4v2-dev \
    libmysqlclient-dev \
    libnet-sftp-foreign-perl \
    libnetpbm10-dev \
    libnumber-bytes-human-perl \
    libpcre3 \
    libpcre3-dev \
    libpolkit-gobject-1-dev \
    libpostproc-dev \
    libssl-dev \
    libswscale-dev \
    libsys-cpu-perl \
    libsys-meminfo-perl \
    libclass-std-fast-perl \
    libdata-uuid-perl \
    libdata-dump-perl \
    libsoap-wsdl-perl \
    libio-socket-multicast-perl \
    libsys-mmap-perl \
    libtheora-dev \
    libtool \
    libv4l-dev \
    libvlc5 \
    libvlccore8 \
    libvlccore-dev \
    libvlc-dev \
    libvorbis-dev \
    libvpx-dev \
    libwww-perl \
    libx264-dev \
    mysql-client \
    mysql-server \
    php \
    php-cli \
    php-gd \
    php-mysql \
    ssmtp \
    software-properties-common \
    vlc-data \
    yasm \
    zip \
    php-apcu-bc && \
    rm -rf /var/lib/apt/lists/*

# Get the latest master branch and submodule(s)
RUN git clone --recursive https://github.com/ZoneMinder/ZoneMinder

# Change into the ZoneMinder directory
WORKDIR /ZoneMinder

# Configure ZoneMinder
RUN cmake .

# Build & install ZoneMinder
RUN make && make install

# ensure writable folders
RUN ./zmlinkcontent.sh

# Set our volumes before we attempt to configure apache
VOLUME /var/lib/zoneminder/images /var/lib/zoneminder/events /var/lib/mysql /var/log/zm

# Stop Apache and Mysql before we configure them
RUN service mysql stop && service apache2 stop

# Configure Apache
RUN cp misc/apache.conf /etc/apache2/sites-available/000-default.conf
RUN echo "ServerName localhost" > /etc/apache2/conf-available/servername.conf && a2enconf -q servername
RUN a2enmod -q cgi && a2enmod -q rewrite

# Configure mysql
RUN echo "sql_mode=NO_ENGINE_SUBSTITUTION" >> /etc/mysql/mysql.conf.d/mysqld.cnf

# Expose http port
EXPOSE 80

# Get the entrypoint script and make sure it is executable
ADD entrypoint.sh /usr/local/bin/
RUN chmod 755 /usr/local/bin/entrypoint.sh

# This is run each time the container is started
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
