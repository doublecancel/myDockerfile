FROM centos:latest
MAINTAINER liuxianglei "liuxianglei@m-chain.com"
RUN yum -y update
RUN yum -y install epel-release
RUN yum -y install redis
RUN yum -y install net-tools
RUN yum -y install vim


COPY redis.conf /redis/redis.conf


EXPOSE 7000
CMD "/usr/bin/redis-server" "/redis/redis.conf" 
