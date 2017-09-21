FROM ubuntu:trusty

MAINTAINER Miguel González <mgonzalez@codicefactory.com>

RUN apt-get update \
&& apt-get install -y wget \
&& apt-get install -y git \
&& apt-get install apt-transport-https \
&& rm -rf /var/lib/apt/lists/*

RUN mkdir /conf
RUN mkdir /logs
RUN mkdir -p /db/sqlite

RUN echo "deb https://www.plasticscm.com/plasticrepo/plasticscm-common/Ubuntu_14.04/ ./" >> /etc/apt/sources.list
RUN echo "deb https://www.plasticscm.com/plasticrepo/plasticscm-latest/Ubuntu_14.04/ ./" >> /etc/apt/sources.list
RUN wget -q https://www.plasticscm.com/plasticrepo/plasticscm-common/Ubuntu_14.04/Release.key -O - | apt-key add -
RUN wget -q https://www.plasticscm.com/plasticrepo/plasticscm-latest/Ubuntu_14.04/Release.key -O - | apt-key add -

RUN DEBIAN_FRONTEND=noninteractive apt-get update \
&& apt-get install -y -q plasticscm-complete \
&& plasticsd stop

RUN { \
    clconfigureserver --language=en --port=8087 --workingmode=UPWorkingMode; \
    [ -f /opt/plasticscm5/server/users.conf ] && mv /opt/plasticscm5/server/users.conf /conf || touch /conf/users.conf; \
    [ -f /opt/plasticscm5/server/groups.conf ] && mv /opt/plasticscm5/server/groups.conf /conf || touch /conf/groups.conf; \
    mv /opt/plasticscm5/server/plasticd.lic /conf; \
    ln -s /conf/users.conf /opt/plasticscm5/server && ln -s /conf/groups.conf /opt/plasticscm5/server; \
    ln -s /conf/plasticd.lic /opt/plasticscm5/server; \
    ln -s /conf/plasticd.token.lic /opt/plasticscm5/server; \
}

RUN umtool cu root root
RUN umtool cg administrators
RUN umtool autg root administrators

ADD loader.log.conf /opt/plasticscm5/server/
ADD db.conf /opt/plasticscm5/server/
ADD gitserver.conf /opt/plasticscm5/server/
EXPOSE 8087
EXPOSE 9418

VOLUME /conf
VOLUME /db/sqlite
VOLUME /logs

CMD ["/opt/plasticscm5/server/plasticd", "--daemon"]
