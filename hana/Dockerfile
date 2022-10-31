FROM registry.suse.com/bci/bci-base:15.4.27.14.5

# Install needed RPMs for HANA
RUN zypper --non-interactive in libatomic1 \
                                insserv-compat \
                                libnuma1 \
                                libaio1 \
                                libltdl7 \
                                gawk \
                                hostname \
                                unzip \
                                tar \
                                gzip

# Install promtail
RUN curl -O -L "https://github.com/grafana/loki/releases/download/v2.6.1/promtail-linux-amd64.zip"
RUN unzip promtail-linux-amd64.zip
RUN cp promtail-linux-amd64 /usr/bin/
RUN mkdir /etc/promtail








