FROM alpine AS build-mencoder

RUN apk add --no-cache subversion git alpine-sdk yasm linux-headers

RUN svn checkout svn://svn.mplayerhq.hu/mplayer/trunk /mplayer

WORKDIR /mplayer

COPY fix-static-build.diff /mplayer

RUN patch < fix-static-build.diff

RUN ./configure --enable-static --disable-mplayer

RUN make -j 4 mencoder

FROM alpine

MAINTAINER Anand Tamariya <atamariya@gmail.com>

RUN apk add --no-cache coreutils ffmpeg mediainfo openjdk8-jre iputils

WORKDIR /usr/src/ums

VOLUME /media

VOLUME /profile

EXPOSE 9001 5002 1044

ENV JVM_OPTS=-Xmx512M

CMD java $JVM_OPTS -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap \
	-DUMS_PROFILE=/profile -Dfile.encoding=UTF-8 -Djava.net.preferIPv4Stack=true -Djna.nosys=true \
	-cp ums.jar net.pms.PMS

COPY . /usr/src/ums

RUN rm -f fix-static-build.diff

COPY --from=build-mencoder /mplayer/mencoder /usr/local/bin/mencoder
