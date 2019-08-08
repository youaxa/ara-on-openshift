FROM docker.io/centos/mysql-57-centos7

COPY ./root/ /

USER 0

RUN chmod 770 /usr/share/container-scripts/mysql/common.sh

USER 27

ENTRYPOINT ["container-entrypoint"]
CMD ["run-mysqld"]