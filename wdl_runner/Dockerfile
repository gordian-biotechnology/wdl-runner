# Copyright 2017 Google Inc.
#
# Use of this source code is governed by a BSD-style
# license that can be found in the LICENSE file or at
# https://developers.google.com/open-source/licenses/bsd

FROM gcr.io/google.com/cloudsdktool/google-cloud-cli:debian_component_based

# Install python
RUN apt-get update && \
    apt-get install wget python3 openjdk-11-jre --yes && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install pip for the next two steps...
RUN apt-get update && \
    apt-get install python3-pip --yes

# Install Python "requests" (for cromwell_driver.py) package
RUN pip3 install requests simplejson

# Install Google Python client (for file_util.py) package
RUN pip3 install --upgrade google-api-python-client

# Remove pip
RUN apt-get remove --yes python3-pip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy the wdl_runner python, shell script, and dependencies
RUN mkdir /wdl_runner
COPY cromwell_driver.py \
     file_util.py \
     sys_util.py \
     wdl_outputs_util.py \
     wdl_runner.py \
     wdl_runner.sh \
     /wdl_runner/
RUN chmod u+x /wdl_runner/wdl_runner.sh

# Copy Cromwell and the Cromwell conf template
RUN mkdir /cromwell
RUN cd /cromwell && \
    curl -L -O https://github.com/broadinstitute/cromwell/releases/download/87/cromwell-87.jar
RUN ln /cromwell/cromwell-87.jar /cromwell/cromwell.jar
COPY jes_template.conf /cromwell/

# Set up the runtime environment
ENV CROMWELL /cromwell/cromwell.jar
ENV CROMWELL_CONF /cromwell/jes_template.conf

WORKDIR /wdl_runner


#docker build -t gcr.io/cromwell-test-237800/wdl_runner:crom87 -f ./Dockerfile ./ && docker push gcr.io/cromwell-test-237800/wdl_runner:crom87