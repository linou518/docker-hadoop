########################################################################
# Dockerfile for building hadoop native libraries
#
#                    ##        .
#              ## ## ##       ==
#           ## ## ## ##      ===
#       /""""""""""""""""\___/ ===
#  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
#       \______ o          __/
#         \    \        __/
#          \____\______/
#
# Component:    Hadoop 2.7
# Author:       pjan vandaele <pjan@pjan.io>
# Scm url:      https://github.com/pjan/docker-hadoop
# License:      MIT
########################################################################

# pull base image
FROM docxs/java:8

# maintainer details
MAINTAINER \
  pjan vandaele "pjan@pjan.io"

# add the build script
COPY bin /bin/

# command is packaging building and packaging the native libraries
CMD \
  ["/bin/build-native"]

# run the docker container for building the native libraries
# > docker build --rm -t docxs/hadoop-native-libs .
# > docker run -it --rm -v <data_folder_for_binaries>:/data docxs/hadoop-native-libs bash
# > build-native
