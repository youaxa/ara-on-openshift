FROM docker.io/centos:7

ARG D_ARG_S2I_scripts_dest="/opt/s2i/destination/s2i/bin"
ARG D_ARG_S2I_destination="/opt/s2i/destination"

ENV JAVA_HOME="/usr/java-sdk"
ENV MAVEN_HOME="/usr/local/maven"
ENV HOME="/opt/java-builds"
#redefined below for subsequent RUN phase
ENV PATH="${PATH}:${JAVA_HOME}/bin:${MAVEN_HOME}/bin"

LABEL description="Docker image used to build the actual application using a modified version for the S2I scripts" \
  io.openshift.s2i.destination=${D_ARG_S2I_destination} \
  io.openshift.s2i.scripts-url=image://${D_ARG_S2I_scripts_dest}

# these will also create the directory tree in the container
COPY ./s2i/bin/ ${D_ARG_S2I_scripts_dest}

# use variable to choose from install or download procedure for JavaSDK
RUN	i_d=$(( [ "x${D_ENV_JavaSDK_download_url}" = "x" ] && echo "i" ) || echo "d") && \
	if [ "d" = $i_d ]; then \
		mkdir -p $JAVA_HOME; \
		echo "---> Downloading java from ${D_ENV_JavaSDK_download_url}"; \
	  cd $JAVA_HOME;  \
	  arch_f=${D_ENV_JavaSDK_download_url##*/}; \
	  sha_f=${D_ENV_JavaSDK_download_sha##*/}; \
	  curl -fsSL ${D_ENV_JavaSDK_download_url} > $arch_f; \
	  if [ "x${D_ENV_JavaSDK_download_sha}" = "x" ]; then \
      echo "---> WARN: No sha location for Java supplied! Cannot verify checksums for download!!!"; \
    else \
		  sum_tool="$(echo ${D_ENV_JavaSDK_download_sha##*.})sum"; \
		  curl -fsSL ${D_ENV_JavaSDK_download_sha} > $sha_f; \
		  echo "---> Verifying checksums for download"; \
		  echo $(cat $sha_f)  $arch_f | $sum_tool -c | grep OK || exit 1; \
		  rm -f $sha_f; \
		fi; \
	  tar -xzC $JAVA_HOME --strip-components=1 -f $arch_f; \
	  rm -rf $arch_f; \
	fi && \
	if [ "i" = $i_d ]; then \
		echo "---> Installing java-1.8.0-openjdk-devel with yum package manager"; \
		yum -y install java-1.8.0-openjdk-devel; \
	  yum clean all; \
	  ln -s $(readlink -f /usr/bin/javac | sed "s/bin\/javac//") $JAVA_HOME; \
	fi && \
	echo "---> Testing java installed correctly" && \
  javac -version && \
  mkdir -p $HOME/.m2 && \
# use variable to choose from install or download procedure for Maven
	i_d=$(( [ "x${D_ENV_Maven_download_url}" = "x" ] && echo "i" ) || echo "d") && \
	if [ "d" = $i_d ]; then \
		mkdir -p ${MAVEN_HOME}; \
		echo "---> Downloading maven from ${D_ENV_Maven_download_url}"; \
		cd $MAVEN_HOME; \
		arch_f=${D_ENV_Maven_download_url##*/}; \
	  sha_f=${D_ENV_Maven_download_sha##*/}; \
		curl -fsSL "${D_ENV_Maven_download_url}" > $arch_f; \
		if [ "x${D_ENV_Maven_download_sha}" = "x" ]; then \
      echo "---> WARN: No sha location for Maven supplied! Cannot verify checksums for download!!!"; \
    else \
			sum_tool="$(echo ${D_ENV_Maven_download_sha##*.})sum"; \
			curl -fsSL ${D_ENV_Maven_download_sha} > $sha_f; \
			echo "---> Verifying checksums for download"; \
			echo $(cat $sha_f) | $sum_tool -c | grep OK || exit 1; \
			rm -f $sha_f; \
		fi; \
		tar -xzC $MAVEN_HOME --strip-components=1 -f $arch_f; \
	  rm -rf $arch_f; \
	  ln -sf ${MAVEN_HOME}/bin/mvn /usr/local/bin/mvn; \
	fi && \
	if [ "i" = $i_d ]; then \
		echo "---> Installing maven with yum package manager"; \
		yum -y install maven; \
		yum clean all; \
	fi && \
  echo "---> Testing maven installed correctly" && \
  mvn -v && \
  echo "---> Done! Maven installed" && \
  chown -R 1001:0 $HOME && \
  chmod -R ug+rwx,o-wx $HOME && \
	chown -R 1001:0 ${D_ARG_S2I_destination} && \
	chmod -R g=u ${D_ARG_S2I_destination} && \
	chown -R 1001:0 ${D_ARG_S2I_scripts_dest} && \
  chmod -R ug+x ${D_ARG_S2I_scripts_dest} && \

WORKDIR $HOME

USER 1001
