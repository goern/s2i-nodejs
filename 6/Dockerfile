FROM openshift/base-centos7

# This image provides a Node.JS environment you can use to run your Node.JS
# applications.

MAINTAINER goern <goern@redhat.com>

EXPOSE 8080

# This image will be initialized with "npm run $NPM_RUN"
# See https://docs.npmjs.com/misc/scripts, and your repo's package.json
# file for possible values of NPM_RUN
ENV NPM_RUN=start \
    NODEJS_VERSION=6.9.5 \
    NPM_CONFIG_PREFIX=$HOME/.npm-global \
    PATH=$HOME/node_modules/.bin/:$HOME/.npm-global/bin/:$PATH

LABEL io.k8s.description="Platform for building and running Node.js 6 applications" \
      io.k8s.display-name="Node.js 6.9.5" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,nodejs,nodejs6,nodejs695" \
      com.redhat.dev-mode="DEV_MODE:false" \
      com.redhat.deployments-dir="/opt/app-root/src" \
      com.redhat.dev-mode.port="DEBUG_PORT:5858"

RUN INSTALL_PKGS="bzip2 nss_wrapper" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum install -y --setopt=tsflags=nodocs https://rpm.nodesource.com/pub_6.x/el/7/x86_64/nodejs-6.9.5-1nodesource.el7.centos.x86_64.rpm && \
    yum clean all -y

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

RUN npm install -g nodemon

# Drop the root user and make the content of /opt/app-root owned by user 1001
RUN chown -R 1001:0 /opt/app-root && chmod -R ug+rwx /opt/app-root
USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
